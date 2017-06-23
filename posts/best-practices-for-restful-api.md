[返回首页](../README.md)

# <a name="top">合理地设计更好的RESTful API（持续更新...）</a>
>如果你看到更好的文章，有更好的点子，或者知道改进方法，请帮助我完善它。

## 目录
- [原则](#h1)
- [URL](#h2)
  - [不使用动词](#h2.1)
  - [使用名词](#h2.2)
  - [使用复数](#h2.3)
  - [关系描述](#h2.4)
  - [不使用修饰词](#h2.5)
- [数据格式](#h3)
  - [application/x-www-form-urlencoded](#h3.1)
  - [Accept和Content-Type（推荐）](#h3.2)
  - [.json/.xml/.html](#h3.3)
  - [不使用修饰词](#h3.4)
  - [命名风格](#h3.5)
  - [格式化和性能](#h3.6)
- [状态码](#h4)
- [数据操作](#h5)
  - [单实体操作](#h5.1)
    - [增](#h5.1.1) 
    - [删](#h5.1.2)
    - [改](#h5.1.3)
    - [查](#h5.1.4)
      - [关系](#h5.1.4)
    - [异常](#h5.1.5)
  - [多实体操作(批量操作)](#h5.2)
    - [增](#h5.2.1) 
    - [删](#h5.2.2)
    - [改](#h5.2.3)
    - [查](#h5.2.4)
      - [过滤](#h5.2.4.1)
      - [排序](#h5.2.4.2)
      - [翻页](#h5.2.4.3)
    - [异常](#h5.2.5)
  - [其他](#h5.3)
- [版本化](#h7)
- [国家化](#h8)
- [资源授权、身份验证、权限验证](#h9)
  - [OAuth 2.0](#h9.1)
- [频率限制](#h10)
- [缓存](h11)
- [HATEOAS](#h12)
- [穿过特殊的网络环境](#h13)
- [Links](#h999)


## <a name="h1">原则</a><sup>[&#x2191;top](#top)</sup>

>任何图形界面作为面向用户的用户接口，都需要考虑用户体验。

>API作为面向开发者的用户接口，同样需要精心的设计。

- 使用合理的网络标准。
- 对开发者友好，能够直接通过浏览器查看。
- 简单、直观、统一，使用起来简单且愉快。
- 足够灵活，适用于绝大多数用户界面。
- 维护成本低。
- 协议文档不以文件形式共享，因为它们仅对创建者友好。（Word, Excel等）
- 协议文档以网页形式共享，因为它们对查阅者友好。

>这篇文章中统一约定“多用户博客系统“作为假设的场景。


## <a name="h2">URL</a><sup>[&#x2191;top](#top)</sup>


### <a name="h2.1">不使用动词</a><sup>[&#x2191;top](#top)</sup>

动作（操作）都是使用GET、POST、PUT、DELETE、PATCH等HTTP方法进行抽象，所以做什么样的操作的信息，干脆别让它出现在URL。

如果你这样做了，反而会给用户增加类似“到底在什么时候用get，什么时候用list，什么时候用fetch?“这种判断成本，这种得不偿失的做法简直是画蛇添足。

```javascript
// Evil!
/getPosts
/getUsers
/listPosts
/fetchAllComments
```


### <a name="h2.2">使用名词</a>[&#x2191;top](#top)

URI叫“统一资源标识符“，任何通过URI(URL)标识的“资源”显然是都是名词，不管它们是什么，都可以理解为继承了“资源”这个超类的各式各样的“子类”。


### <a name="h2.3">使用复数</a><sup>[&#x2191;top](#top)</sup>

尽管第一印象中，用复数名词表示单数对象看上去很奇怪，但复数在很多场景下都被证明是最佳选择。

例如:
>当从一个博客系统中标识一个帖子对象的时候，帖子不可能只有一个。所以使用/posts/123这种URL非常符合逻辑，因为它表达“在多个帖子中选择编号为123的那个帖子”，

你设计的时候，没有必要把持久层（关系型数据库）的抽象原封不动地映射到API的设计上。

API和数据库层虽然都是对应用中的各种实体对象，以及它们的属性和行为进行抽象，但数据库和API是完成不同的职责，也与不同职责的分层的进行通信。理清当前上下文的职责是什么，边界在哪里是在任何一项软件设计工作中都是重点和难点，同时那是你的工作！

一律使用复数名词，同时也可以避免API文档的查阅者产生“什么时候是单数，什么时候是复数？”的疑惑。

>people这种名词出现在API的URL上，会让使用者困惑到底是“人”还是“人民”。当你系统中既出现“人“的抽象，同时也出现”人民“的抽象的时候，为了减少双方的麻烦，你可以考虑牺牲英语中精确的词法，来换API表达的直观和易懂。遇到单数和复数是以特殊方式出现在英语词法中的名词时，你需要非常谨慎。

```javascript
// Good!
/blogs/123/posts/123/comments/456
/users/123/posts/123
```


### <a name="h2.4">关系描述</a><sup>[&#x2191;top](#top)</sup>

关系可以用如下URL的组织方式表达。

```javascript
// 下方两个URL中只出现了两种相同的实体对象，但两种URL表达的含义是不一样的。
/tags/456/posts // 列出拥有456号标签的所有帖子。
/posts/123/tags // 列出编号为123的帖子的所有标签。
```

我们每天都在使用的域名也是这种方式组织信息的。只不过域名是从后到前缩小领域，URL是从前到后缩小领域。

>news.baidu.com: 所有商业网站中的，百度公司的，新闻频道。

希望上面这个域名语义拆解，对你有帮助。

在实际应用中，很多复杂的关系是没有办法使用这种简单的办法表达出来，只能借助HTTP的Body内容来描述。

见[利用Body内容描述关系](#h3.7)


### <a name="h2.5">不使用修饰词</a><sup>[&#x2191;top](#top)</sup>

不要对资源实体对象进行修饰描述。

如果没有这项限制，你很难阻止你的团队的成员在将来定义出postItems、postEntitis、commentObjects等千奇百怪的命名，最后导致你的团队成员们对实体对象的命名没办法有稳定的预期。

```javascript
// Evil!
/usersList // List是废话，应为/users
/tagItems // Items是废话，应为/tags
```


## <a name="h3">数据格式</a><sup>[&#x2191;top](#top)</sup>
>强烈建议统一使用application/json，而且它应该成为默认的交互协议。

### <a name="h3.1">application/x-www-form-urlencoded</a><sup>[&#x2191;top](#top)</sup>

很多人误认为这是HTTP的“默认”数据类型。

实际上在请求阶段会把表单项键值对拼接成key1=value1&key2[]=value21&key2[]=value22这种字符串放在Body中发送。

它并不是什么HTTP的“默认”数据格式，而是早些年实现的浏览器的默认行为实现。这个默认行为也要看具体是什么样的表单项，如果是大文件上传，这个格式可能会变成multipart/form-data，并不是application/x-www-form-urlencoded。因此，事实上没有什么所谓HTTP的默认格式。

最新的Javascript框架或库，以angular为例，也已经不再把老式浏览器的默认MIME type作为自己默认的数据格式，而是转而使用application/json，或者让开发者自己定义。

application/x-www-form-urlencoded由于受限于它自身的定义，尤其描述复杂的数据关系的时候是无能为力。

目前，不管是服务端还是客户端，正确处理不同的MIME type，在技术上已经没有任何障碍。因此，我们更没有理由在RESTful API上使用表达能力非常有限的application/x-www-form-urlencode作为默认格式，是时候把这个类型丢在脑后了。


### <a name="h3.2">Accept和Content-Type（推荐）</a><sup>[&#x2191;top](#top)</sup>

| 类型 | HTTP头 | 说明 | 实例 |
| --- | --- | --- | --- |
| 请求 | Accept | 客户端能接收处理的格式。 | Accept: application/json,application/xml; |
| 响应 | Content-Type | 服务端响应的格式，同时需要定义字符集。 | Content-Type: application/json; charset=utf-8 |

常用类型见[MIME types](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types)。

HTTP协议中定义Accept和Content-Type头，正是为了达到客户端和服务端能够协商MIME type的目的，因此使用它们非常合乎逻辑，同时也能能够让你的URL更佳干净。

再则，现在对任何一个开发者来讲，使用Chrome或者Firefox等浏览器的插件，已经不是什么负担，而是常识。

下面是可以构造HTTP请求的浏览器插件。

- Chrome: [Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop?utm_source=chrome-ntp-icon), [Advanced REST client](https://chrome.google.com/webstore/detail/advanced-rest-client/bljmokabgbdkoefbmccaeficehkmlnao)
- Firefox: [RESTClient, a debugger for RESTful web services](https://addons.mozilla.org/en-US/firefox/addon/restclient/)

### <a name="h3.3">.json/.xml/.html</a><sup>[&#x2191;top](#top)</sup>

```javascript
/posts/2341/comments/5543.json
/posts/2341/comments.xml?user_id=3244&rate=5
```

通过这种方式，可以在不依赖第三方工具的前提下，直接在浏览器中查看返回数据的不同格式。

HTTP头这样的信息是，要么通过浏览器插件，要么通过程序中才有办法定义，用户是没有其他途径。

缺点是服务端需要解析URL中的后缀信息，但是如果你的程序分层和抽象做的好，那么你可以用AOP的方式为你的API增加这种能力。

以增量的方式对已有的API进行适配升级，升级的时候把原来使用的格式作为默认格式，其实代价不会大到无法接受。


### <a name="h3.4">不使用修饰词</a><sup>[&#x2191;top](#top)</sup>

与在URL避免用废话修饰的理由相同

```javascript
// Evil!
{
    "info": { // 这里info是废话
        "id": 134,
        "title": "我是标题内容",
        ...
    },
}
// Evil!
{
    "data": { // 这里data是废话
        "id": 134,
        "title": "我是标题内容",
        ...
    },
}
// Good!
{
    "id": 134,
    "title": "我是标题内容",
    ...
}
```

有时，这种“信封信息”(Envelope)并不完全是“废话”。

例如:
- 使用total来为翻页功能提供必要的信息。
- 为jsonp形式的请求返回不同的callback表达式。
```javascript
{
    "total": 123432, // 总帖子数
    "entities": { // 这里entities的意义是避免实体对象的数据与total等meta信息散列在同一级
        "posts": [
            {
                "id": 134,
                "title": "我是标题内容",
                ...
            },
            ...
        ],
    }
    "callback": "renderPostListPage(posts, total_count, 344)",
    ...
}
```

尽管如此，从目前发展来看，[CORS](http://www.w3.org/TR/cors/)或者[The Link Header Field(RFC5988)](http://tools.ietf.org/html/rfc5988#page-6)这种新标准的应用在绝大多数场景下都可以满足。因此，这种修饰方式封装实体数据的做法，应尽可能避免。

对服务没有配置权限的，很难处理跨域请求，只好使用jsonp的方式。

但从职责来看，这是API服务的提供方有没有良好的服务意识的问题，因此应优先考虑在服务方得到解决。


### <a name="h3.5">命名风格</a><sup>[&#x2191;top](#top)</sup>

一般来讲，API是要遵守客户端语言的命名规范。

例如:
- Javascript中通常使用camelCase。
- Python中通常使用snake_case。
- PHP可能使用camelCase，也有可能使用snake_case。
- Java使用camelCase。

所以，选择一种风格，始终如一地使用同一种规范即可。

>据统计snake_case风格比camelCase要容易阅读20%左右!


### <a name="h3.6">格式化和性能</a><sup>[&#x2191;top](#top)</sup>

> ?_pretty=true

这里的格式化是指使用空白符缩进进行格式化的对人友好的数据展现。因此同样考虑对人友好，HTTP头等其他手段都会增加成本，只有URL的GET参数是最小成本的，同时实现它的代价也是微乎其微。

这种格式化由于包含了很多空白符，因此一定会增加HTTP数据包的大小。

通常对中等规模的数据来讲，这种格式化带来的大小上的差异在10%以内，而使用gzip压缩的情况下，可能连3%都不到。同时目前绝大多数应用中，响应数据是默认使用gzip进行压缩，而且这种压缩往往都是对客户端透明，更值得一提的是gzip压缩情况下甚至会带来60%以上的大小上的优化，压缩效果是非常显著的，因此格式化带来的大小增长，大部分情况下可以忽略不计。

如果你的场景需要用非常极限的压缩，换来更佳极致的性能，那么application/json格式本身就不是你的最佳选择，因为json毕竟是使用字符串类型表示所有数据，同时它还包含了很多《"》。这种情况下，你可能更愿意让服务端和客户端使用二进制的自定义的MIME type来进行数据交互。

如果是为第三方提供开放API的服务，建议你最好增加这种机制，你的用户心里会默默感激你的良苦用心。

如果都是自己人使用，最好让使用者都使用浏览器插件查看格式化后的数据，因为服务端增加pretty=true的适配，或多或少都是成本。

查看格式化json的浏览器插件。

- Chrome: [JSON Editor](https://chrome.google.com/webstore/detail/json-editor/lhkmoheomjbkfloacpgllgjcamhihfaj)
- Chrome: [JSON Formatter](https://chrome.google.com/webstore/detail/json-formatter/bcjindcccaagfpapjjmafapmmgkkhgoa)
- Firefox: [JSONView](https://addons.mozilla.org/en-us/firefox/addon/jsonview/)


## <a name="h4">状态码</a><sup>[&#x2191;top](#top)</sup>

HTTP状态码很多，可以看[List of HTTP status codes](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)了解一下。 

使用HTTP的状态码来描述状态，服务端和客户端都有需要分别处理它们的代价。

下方列表为几个候选状态码。

挑选个别几个在团队内部约定好即可，同时只靠HTTP的状态码是不足以描述清楚所有情况。

所有状态码都只有客户端针对不同的情况能够做出不同的反馈的时候才会有意义。

| Code | 默认返回文本 | 说明 |
| --- | --- | --- |
| 200 | OK | 一切正常。|
| 201 | Created | 与POST搭配使用。 |
| 204 | No Content | 与DELETE搭配使用。 |
| 304 | Not Modified | 对于调用频率非常高，应用了服务端缓存技术的API来讲使用要使用此状态码告知客户端使用的是缓存中的数据是必不可少的。 |
| 400 | Bad Request | 服务端要返回尽可能详细的错误反馈。见[下一节](#h4.2)。 |
| 401 | Unauthorized | 对需要身份验证的API必不可少。见[资源授权、身份验证、权限验证](#h9) |
| 403 | Forbidden | 服务端有能力处理请求，但是根据目前授权不允许响应操作。与401配套使用。 |
| 404 | Not found | 请求的资源不存在。非常实用。 |
| 405 | Method Not Allowed | 无法用请求的方法对目标资源进行操作。 |
| 410 | Gone | 废弃API可以用它告知客户端。 |
| 415 | Unsupported Media Type | API不支持处理该格式数据。例如向只支持json数据的API发送了xml格式的数据。 |
| 422 | Unprocessable Entity | 表单验证失败可以用这个状态码。例如客户端提交数据符合协议，但某些字段之间的逻辑关系可能有问题。  |
| 429 | Too Many Requests | 限制频率的API应该使用它。 |
| 500 | Internal Server Error | 任何一个API都应该避免让web服务返回5XX的状态码。不应让客户端能够感知到服务端配置错了或者写错了代码。 |


## <a name="h5">数据操作</a><sup>[&#x2191;top](#top)</sup>

>数据操作可以抽象为最基本的5类：增、删、改、查、搜。

>你还需要了解《[异常详情](#h4.2)》两节。

### <a name="h5.1">单实体操作</a><sup>[&#x2191;top](#top)</sup>

#### <a name="h5.1.1">增</a><sup>[&#x2191;top](#top)</sup>

相当于SQL中的INSERT。

创建对象的时候由服务端产生的“创建时间、默认分类“等部分属性是客户端可能无法知道的。

因此，建议返回新创建的实体对象。

成功时状态码：**201**

```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/blogs
// Request Method: POST
// Accept: application/json
// Accept-Language: zh-CN
{
  "user_id": 12,
  "title": "合理的设计",
  "sub_title": "整理并且持续更新各种合理的设计约定",
}
```

```javascript
// 响应
// Status Code: 201 Created
// Content-Type: application/json; charset=utf-8
// Content-Encoding: gzip
{
  "id": 123
  "user_id": 12,
  "title": "合理的设计",
  "sub_title": "整理并且持续更新各种合理的设计约定",
  "insert_datetime_6": "2017-02-14T11:29:54.326386+08:00" // 见页面底部Links中的ISO 8601
}
```

#### <a name="h5.1.2">删</a><sup>[&#x2191;top](#top)</sup>

这个操作逻辑上也能知道只能成功一次。

对后续多次请求应返回“对象不存在”的状态。

成功时建议使用HTTP状态码：**204**

建议不要返回实体数据。

```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/blogs/123
// Request Method: DELETE
// Accept: application/json
// Accept-Language: zh-CN
```

```javascript
// 响应
// Status Code: 204 No Content
// Content-Type: application/json; charset=utf-8
// Content-Encoding: gzip
//
// 想在删除后返回被删除对象，是需要至少执行两个SQL语句。
// 一般删除操作向最终用户反馈删除成功与否即可。
// 事实上用户确认自己要删除的目标对象那一步，往往是需要在提交删除操作之前要完成。
```

#### <a name="h5.1.3">改</a><sup>[&#x2191;top](#top)</sup>

幂等方式修改已有的数据。

相当于SQL中的UPDATE，HTTP Body相当于SET的参数，而URL相当于WHERE。

成功时建议使用HTTP状态码：**200**

建议返回被更改的实体对象数据。


```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/blogs/123
// Request Method: PUT
// Accept: application/json
// Accept-Language: zh-CN
{
  "user_id": 12,
  "title": "用心设计",
  "sub_title": "整理各种设计约定",
}

// 响应
// Status Code: 200 Ok
// Content-Type: application/json; charset=utf-8
// Content-Encoding: gzip
{
  "id": 123
  "user_id": 12,
  "title": "用心设计",
  "sub_title": "整理各种设计约定",
  "insert_datetime_6": "2017-02-14T11:29:54.326386+08:00",
  "update_datetime_6": "2017-02-14T11:50:02.231576+08:00"
}
```

#### <a name="h5.1.4">查</a><sup>[&#x2191;top](#top)</sup>

相当于SQL中的SELECT。而URL则相当于WHERE。

```javascript
/posts/2341/comments/5543
// 1. 所有帖子中找到编号为2341的帖子。
// 2. 在这个帖子的所有评论中，找到编号为5543的评论。

/posts/2341/comments?user_id=3244&rate=5
// 1. 所有帖子中找到编号为2341的帖子。
// 2. 在这个帖子的所有评论中，找到用户编号为3244，并且被赞5次的所有评论。
```

成功时建议使用HTTP状态码：**200**

一定返回实体数据。


```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/blogs/123/posts
// Request Method: GET
// Accept: application/json
// Accept-Language: zh-CN

// 响应
// Status Code: 200 Ok
// Content-Type: application/json; charset=utf-8
// Content-Encoding: gzip
{
  "id": 35135,
  "blog_id": 123,
  "tags": [12, 17, 34, 25],
  "title": "合理的设计RESTful API",
  "content": "..."
  "insert_datetime_6": "2017-02-14T11:29:54.326386+08:00",
  "update_datetime_6": "2017-02-14T11:50:02.231576+08:00"
}
```

##### <a name="h5.1.4.1">关系</a><sup>[&#x2191;top](#top)</sup>
如果，你已经阅读到这里，那么心里应该已经有很大的困惑：
>我的API要表达多种实体数据，而且还要把它们的关系表达出来，这个怎么办？

所以，我把这种关系的描述，放在了“复杂操作”中的第一个。同时，其他操作中，多少都会涉及到关系的描述。

如[前面](#h2.4)提到，[URL中描述关系](#h2.4)的时候很难描述实体关系比较复杂的情况。

而Body内容中，描述这种关系，也不是那么简单。有几种选择。

- 所有API都只操作单个实体对象。
  这意味着由非常多的简单API构成系统，同时会导致只有依赖很多次API请求才能完成很多业务逻辑。
  而这对受限于有限的HTTP连接数的浏览器应用来讲，相应的异步操作的等待时间会增加。
  没有良好的交互设计，可能会给用户带来非常糟糕的体验。
- 所有API都返回固定的数据结构，实体之间的关系用嵌套对象来描述，这样实现起来比较简单。
  但是，实体之间的关系比较复杂的系统中，这种方式会带来很多交互数据上的冗余。
  而这种冗余意味着很多场景下，实际的HTTP数据包要比真正需要的数据包要大。
  同时，在某些实体对象到底通过A接口，还是B接口的选择上，非常考验设计者在很高层次上的抽象能力。
- 最后一个选项，从逻辑上，是最佳方案。但实现代价也最高。
  就是让客户端来决定是否获取关联的实体对象，如果获取，用什么方式组织响应体的结构。
  下面是这种方案的实现方法。在数据获取方式的描述上，要增加约定好的GET参数。
  个人更佳倾向外链方式。

>内嵌方式：/posts/123?_embed=user,tags

```javascript
// 内嵌方式
{
  "id": 123
  "user": {
    "id": 32423,
    "username": "hanzhixing",
    "email": "zhixing.han.0409@gmail.com",
  }
  "tags": [
    {
      "id": 23,
      "name": "restful",
    },
    {
      "id": 12,
      "name": "convention",
    }
  ],
  "title": "用心设计",
  "content": "...",
  "insert_datetime_6": "2017-02-14T11:29:54.326386+08:00",
  "update_datetime_6": "2017-02-14T11:50:02.231576+08:00"
}
```

>外链方式：/posts/123?_extend=user,tags

```javascript
// 外链方式
{
  "post": {
    "id": 123
    "user_id": 32423,
    "title": "用心设计",
    "tags": [12, 23],
    "content": "..."
    "insert_datetime_6": "2017-02-14T11:29:54.326386+08:00",
    "update_datetime_6": "2017-02-14T11:50:02.231576+08:00"
  },
  "user": {
    "id": 32423,
    "username": "hanzhixing",
    "email": "zhixing.han.0409@gmail.com",
  },
  "tags": [
    {
      "id": 12,
      "name": "convention",
    },
    {
      "id": 23,
      "name": "restful",
    },
    // ...
  ],
}
```

##### <a name="h5.1.4.2">字段挑选</a><sup>[&#x2191;top](#top)</sup>

>可以按需获取所需要的信息，减少通信数据的大小。需要约定。

```javascript
// 请求
// Request Method: GET
// Accept: application/json
// _field: 字段列表，英文“,”分割。
/posts?_field=id,title,tags.id,tags.name&_extend=tags
```

#### <a name="h5.1.5">异常</a><sup>[&#x2191;top](#top)</sup>

API为客户端反馈异常情况的时候，往往状态码和简单的一句描述，是远远不够的。

例如客户端提交注册表单，希望能够友好地提示用户哪些表单项填错了，是怎么错的。

又例如API是接收多个实体对象，服务端操作的时候检测到部分实体对象的操作由于某种原因无法完成。
这种情况下也要告知客户端到底发生什么事情了，只有这样客户端才能够对用户反馈有价值的提示或警告信息。

经在不同场景试用，如下这种方案比较实用。

```javascript
// 单实体
{
  "error": {
    // code字段根据实际情况可以不使用。
    // 服务端想返回的消息内容和客户端希望使用的消息内容不一样的场景较多的时候，尽量使用。
    // 比如，如果msg内容足以帮助客户端为用户提供友好的反馈或者基于它直接进行国际化等处理，那么你可以不使用它。
    "code": 422, 
    // 消息内容在任何时候都不应该省略，一是服务端从记录日志角度可能需要它，客户端也有可能直接使用它的内容。
    "msg": "Invalid characters in username",
    // 以上是异常情况的总体描述，除此之外，你往往还需要资源每个属性项的异常信息。
    "detail": [ // 可选
      {
        "key" : "username",
        "code" : 422001,
        // 警告！如果考虑OAuth这种标准，msg里边应该只返回英文描述。
        "msg" : "The 'username' can only contains alphabets, numbers and '_'."
      }, // 同时对同一个字段，针对不同的异常情况，也会有不同的错误码和消息内容。
      ...
    ],
  }
}
```

### <a name="h5.2">多实体操作(批量操作)</a><sup>[&#x2191;top](#top)</sup>

#### <a name="h5.2.1">增</a><sup>[&#x2191;top](#top)</sup>

```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/blogs
// Request Method: POST
// Accept: application/json
// Accept-Language: zh-CN
[
  {
    "user_id": 12,
    "title": "合理的设计",
    "sub_title": "整理并且持续更新各种合理的设计约定",
  },
  {
    "user_id": 12,
    "title": "合理的设计",
    "sub_title": "整理并且持续更新各种合理的设计约定",
  }
]
```

```javascript
// 响应
// Status Code: 201 Created
// Content-Type: application/json; charset=utf-8
// Content-Encoding: gzip
[
  {
    "id": 123
    "user_id": 12,
    "title": "合理的设计",
    "sub_title": "整理并且持续更新各种合理的设计约定",
    "insert_datetime_6": "2017-02-14T11:29:54.326386+08:00" // 见页面底部Links中的ISO 8601
  },
  {
    "id": 123
    "user_id": 12,
    "title": "合理的设计",
    "sub_title": "整理并且持续更新各种合理的设计约定",
    "insert_datetime_6": "2017-02-14T11:29:54.326386+08:00" // 见页面底部Links中的ISO 8601
  }
]
```

#### <a name="h5.2.2">删</a><sup>[&#x2191;top](#top)</sup>

隐含“多实体”的“查”操作。

```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/blogs?id=123,456,789
// Request Method: DELETE
// Accept: application/json
// Accept-Language: zh-CN
```

```javascript
// 响应
// Status Code: 204 No Content
// Content-Type: application/json; charset=utf-8
// Content-Encoding: gzip
```

#### <a name="h5.2.3">改</a><sup>[&#x2191;top](#top)</sup>

```javascript
// 请求
// Request URL: https://api.domain.com/rest/v1/blogs
// Request Method: PUT
// Accept: application/json
// Accept-Language: zh-CN
[
  {
    "user_id": 12,
    "title": "合理的设计",
    "sub_title": "整理并且持续更新各种合理的设计约定",
  },
  {
    "user_id": 12,
    "title": "合理的设计",
    "sub_title": "整理并且持续更新各种合理的设计约定",
  }
]
```

```javascript
// 响应
// Status Code: 200 Ok
// Content-Type: application/json; charset=utf-8
// Content-Encoding: gzip
[
  {
    "id": 123
    "user_id": 12,
    "title": "合理的设计",
    "sub_title": "整理并且持续更新各种合理的设计约定",
    "insert_datetime_6": "2017-02-14T11:29:54.326386+08:00",
    "update_datetime_6": "2017-02-14T11:50:02.231576+08:00"
  },
  {
    "id": 123
    "user_id": 12,
    "title": "合理的设计",
    "sub_title": "整理并且持续更新各种合理的设计约定",
    "insert_datetime_6": "2017-02-14T11:29:54.326386+08:00",
    "update_datetime_6": "2017-02-14T11:50:02.231576+08:00"
  }
]
```

#### <a name="h5.2.4">查</a><sup>[&#x2191;top](#top)</sup>

>它们是RESTful API的设计难点

##### <a name="h5.2.4.1">过滤</a><sup>[&#x2191;top](#top)</sup>

```javascript
// 请求
// Request Method: GET
// Accept: application/json
// =, !=, >=, <=
/posts?id=123,456&user_id>=16&tag_id!=12,22
```

##### <a name="h5.2.4.2">排序</a><sup>[&#x2191;top](#top)</sup>
```javascript
// 请求
// Request Method: GET
// Accept: application/json
// 无符号表示正排，英文“-”表示倒排，英文“,”分割字段，顺序影响排序结果。
/posts?_sort=user_id,-update_datetime,tag_id
```

##### <a name="h5.2.4.3">翻页</a><sup>[&#x2191;top](#top)</sup>

```javascript
// 请求
// Request Method: GET
// Accept: application/json
// _offset表示数据便宜量，_limit表示查询个数
/posts?_offset=2400&_limit=20
```

```javascript
// 响应（推荐）
// 需要告知客户端总共还有多少数据，要不然页面上无法显示总页数。Link客户端往往可以自行拼接。
// X-Total-Count: 3243
// Link: </posts?_offset=15&_limit=5>; rel="next",</posts?_offset=50&_limit=3>; rel="last",</posts?_offset=0&_limit=5>; rel="first",</posts?_offset=5&_limit=5>; rel="prev"
```

```javascript
// 响应（需要信封）
// 优点是不需要自行处理Header，缺点是可能的命名冲突，结构也会多一层。
{
  total: 1343,
  entities: [
    {
        ...
    },
    ...
  ],
}
```

#### <a name="h5.2.5">异常</a><sup>[&#x2191;top](#top)</sup>
```javascript
{
  // 实体key。
  // 仅返回操作成功的实体对象。如果所有实体对象都没有被成功操作，则不返回这个对象。
  "entities": [
    {
      "id": 3432,
      "title": "Any useful text",
    },
    ...
  ],
  // 仅在发生异常时返回errors，如果没有发生异常，则不返回这个对象。
  "errors": [
    {
      "index": 1, // 提交时的数组下标
      "code": 422, 
      "msg": "Invalid characters in username",
      // 以上是异常情况的总体描述，除此之外，你往往还需要每个表单项的异常信息。
      "detail": [
        {
          "key" : "username",
          "code" : 422001,
          "msg" : "The 'username' can only contains alphabets, numbers and '_'."
        },
        ...
      ],
    },
    ...
  ]
}
```

### <a name="h5.3">其他</a><sup>[&#x2191;top](#top)</sup>

API设计过程当中，也会碰到一些无法用以上的操作无法描述的操作。

这类API通常都需要专门的使用说明。

例如：
- 激活操作。可以用PATCH提交{activated: false}。
  但往往可以通过把activated属性抽象成目标资源的属性的方式来解决。
- 收藏、取消收藏。
  收藏对象也可以是一种资源。
- 跨对象的搜索。
  关键字来进行搜索，但可能会匹配文章，可能会匹配评论，也有可能会匹配新闻公告等。
  这种情况下是没有办法事先明确资源。因此系统提供/search这种专用的URL是比较可取的。

## <a name="h7">版本化</a><sup>[&#x2191;top](#top)</sup>
>版本化会为你将来的重构、升级、并存等创造条件。

当然同时需要利用对域名的良好的命名为不同类型的资源划分好领域。

版本规范见[Semantic Versioning 2.0.0](http://semver.org/)。

API对任何应用，在分层角度上都是偏“底层”的“数据源”层。不大可能在双方没有预定的情况下随意增加删除字段。

但它们是也和任何一种程序一样，不可能永远不变。随着时间的推移，一定会需要升级更新。

版本化为API的平滑重构、流量控制、多版本适配提供很多便利。同时也为API的用户提供方便。

```javascript
// MAJOR位前置
/rest/v1/...
/api/v3/...
// MINOR和PATCH位使用query参数
/rest/v1/posts/123?_ver=v1.1
/rest/v1/posts/123?_ver=v1.1.5
```

## <a name="h8">国际化</a><sup>[&#x2191;top](#top)</sup>

```javascript
// 请求
// Accept-Language: zh-CN;q=0.8,zh;q=0.6,en-US;q=0.4,en;q=0.2 
```

```javascript
// 响应
// Content-Language: zh-CN
```

## <a name="h9">资源授权、身份验证、权限验证</a><sup>[&#x2191;top](#top)</sup>

### <a name="h9.1">OAuth 2.0</a><sup>[&#x2191;top](#top)</sup>

TODO

## <a name="h10">频率限制</a><sup>[&#x2191;top](#top)</sup>
TODO

X-Rate-Limit-Limit, X-Rate-Limit-Remaining, X-Rate-Limit-Reset 


## <a name="h11">缓存</a><sup>[&#x2191;top](#top)</sup>

TODO

## <a name="h12">HATEOAS</a><sup>[&#x2191;top](#top)</sup>
>**H**ypermedia **a**s **t**he **E**ngine **o**f **A**pplication **S**tate

为客户端提供利用API进一步查找数据的附加信息。
在应用这项原则之前，需要评估一下API的客户端是否是在运行时刻根据服务端响应的数据动态地决定进一步的动作。
绝大多数网页都不是运行时刻决定，而是开发时刻由程序员写响应的代码来决定的。
因此我们的世界可能还没有准备好大面积使用HATEOAS原则。

```javascript
{
    ...
    "links": [
    {
        "rel": "self",
        "href": "/api/v1/posts/23",
        ...
    },
    ...
}
```

## <a name="h13">穿过特殊的网络环境</a><sup>[&#x2191;top](#top)</sup>

>网络环境中有的代理服务可能只支持GET和POST方法

服务端最好利用自定义的HTTP Header来识别PUT、DELETE等请求。

```javascript
// 请求
// Request Method: GET
// X-HTTP-Method-Override: PUT
```

## <a name="h999">Links</a><sup>[&#x2191;top](#top)</sup>

- [ISO 8601](./iso-8601.md)
- [MIME types](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types)。
- [Media type](https://en.wikipedia.org/wiki/Media_type)
- [List of HTTP status codes](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)
- [List of HTTP header fields](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields)
- [HTTP Request methods](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#Request_methods)
- [Best Practices for Designing a Pragmatic RESTful API](http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api)
- [10 Best Practices for Better RESTful API](http://blog.mwaysolutions.com/2014/06/05/10-best-practices-for-better-restful-api/)
- [json-server](https://github.com/typicode/json-server)
- [API Design Cheat Sheet](https://github.com/RestCheatSheet/api-cheat-sheet#api-design-cheat-sheet)
- [Platform-Building Cheat Sheet](https://github.com/RestCheatSheet/platform-cheat-sheet#platform-building-cheat-sheet)
- [RESTful API Best Practices and Common Pitfalls](https://medium.com/@schneidenbach/restful-api-best-practices-and-common-pitfalls-7a83ba3763b5#.a4zxx9nlr)
- [Proper REST response for empty table?](http://stackoverflow.com/questions/13366730/proper-rest-response-for-empty-table)
- [What is the proper REST response code for a valid request but an empty data?](http://stackoverflow.com/questions/11746894/what-is-the-proper-rest-response-code-for-a-valid-request-but-an-empty-data)
- [Amazon S3 REST API Introduction](http://docs.aws.amazon.com/AmazonS3/latest/API/Welcome.html)
- [Building REST services with Spring](https://spring.io/guides/tutorials/bookmarks/)
- [React.js and Spring Data REST](https://spring.io/guides/tutorials/react-and-spring-data-rest/)



