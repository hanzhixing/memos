# <a name="top">合理地设计更好的RESTful API</a>

>如果你看到更好的文章，有更好的点子，或者知道改进方法，请帮助我完善它。

## 目录
- [设计原则](#h1)
- [URL](#h2)
  - [不使用动词](#h2.1)
  - [使用名词](#h2.2)
  - [使用复数](#h2.3)
  - [关系描述](#h2.4)
  - [不使用修饰词](#h2.5)
- [数据格式](#h3)
  - [application/x-www-form-urlencoded](#h3.1)
  - [利用URL中的资源后缀（推荐）](#h3.2)
  - [利用HTTP头Accept和Content-Type描述](#h3.3)
  - [避免用无意义修饰信息包装实体对象](#h3.4)
  - [命名风格](#h3.5)
  - [格式化和性能](#h3.6)
- [状态反馈](#h4)
  - [状态码](#h4.1)
  - [异常详情](#h4.2)
- [数据操作](#h5)
  - [增](#h5.1) 
  - [删](#h5.2)
  - [改](#h5.3)
  - [查](#h5.4)
  - [搜](#h5.5)
- [复杂操作](#h6)
  - [过滤](#h6.1)
    - [利用URL](#h6.1.1)
    - [利用POST的Body](#h6.1.2)
  - [排序](#h6.2)
  - [字段挑选](#h6.3)
  - [翻页](#h6.4)
    - [Request](#h6.4.1)
      - [利用URL](#h6.4.1.1)
      - [利用自定义Header](#h6.4.1.2)
    - [Response](#h6.4.2)
      - [利用HTTP Header](#h6.4.2.1)
      - [利用HTTP Body](#h6.4.2.2)
  - [其他](#h6.5)
- [版本化](#h7)
- [国家化](#h8)
- [资源授权、身份验证、权限验证](#h9)
  - [OAuth 2.0](#h9.1)
- [HATEOAS](#h10)
- [穿过特殊的网络环境](#h11)


## <a name="h1">设计原则</a><sup>[&#x2191;top](#top)</sup>

>我们都知道任何图形界面作为面向用户的用户接口，都需要考虑用户体验。
>API作为面向开发者的用户接口，同样需要精心的设计。

- 使用合理的网络标准。
- 对开发者友好，并且可以通过浏览器地址栏可以浏览到。
- 简单、直观、统一，以此保证它使用起来简单且愉快。
- 足够灵活，适用于绝大多数用户界面。
- 维护成本低。


## <a name="h2">URL</a><sup>[&#x2191;top](#top)</sup>


### <a name="h2.1">不使用动词</a><sup>[&#x2191;top](#top)</sup>

动作（操作）都是使用GET、POST、PUT、DELETE、PATCH等HTTP方法进行抽象，所以做什么样的操作，你是没有必要在URL中表达出来。
如果你这样做了，反而会给使用者增加类似“我应该在什么时候用get，什么时候用list?“这种判断成本。

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

尽管第一印象中，单数对象用复数表达看上去很奇怪，但复数在很多场景下都被证明是最佳选择。

例如:
>从一个博客系统中标识一个帖子对象的时候，帖子不可能只有一个。所以使用/posts/123这种URL非常符合逻辑，因为它表达“在多个帖子中选择编号为123的那个帖子”，

不过，你设计的时候，没有必要把持久层（关系型数据库）的抽象原封不动地映射到API的设计上。

API和数据库层虽然都是对应用中的各种对象进行抽象，然后再对它们的属性和行为进行命名，但实际应用中数据库和API是完成不同的职责，也与不同职责的分层的进行通信。理清当前上下文的职责是什么，边界在哪里是在任何一项软件设计工作中都是重点和难点。那是你的工作，不是像本篇文档一样的约定能解决的。

一律使用复数名词，同时也可以避免API文档的查阅者产生“什么时候是单数，什么时候是复数？”的疑惑。

>people这种名词出现在API的URL上，会让使用者困惑到底是“人”还是“人民”。
>当你系统中既出现“人“的抽象，同时也出现”人民“的抽象的时候，为了减少双方的麻烦，你可以考虑牺牲英语中精确的词法，来换API表达的直观和易懂。

```javascript
// Good!
/blogs/123/posts/123/comments/456
/users/123/posts/123
```


### <a name="h2.4">关系描述</a><sup>[&#x2191;top](#top)</sup>

关系可以用如下URL的组织方式表达。

```javascript
/tags/456/posts
// 列出拥有456号标签的所有帖子。
/posts/123/tags
// 列出编号为123的帖子的所有标签。
```

我们每天都在使用的域名也是这种方式组织信息的。只不过域名是从后到前缩小领域，URL是从前到后缩小领域。

>news.baidu.com: 所有商业网站中的，百度公司的，新闻频道。

在实际应用中，很多复杂的关系是没有办法使用这种简单的办法表达出来，只能借助HTTP的Body内容来描述。

**TODO**: 利用Body的关系描述


### <a name="h2.5">不使用修饰词</a><sup>[&#x2191;top](#top)</sup>

如果没有这项限制，你很难阻止你的团队的成员在将来定义出postItems、postEntitis、commentObjects等千奇百怪的命名，最后导致你的团队成员们对实体对象的命名没办法有稳定的预期，

```javascript
// Evil!
/usersList // List是废话，应为/users
/tagItems // Items是废话，应为/tags
```


## <a name="h3">数据格式</a><sup>[&#x2191;top](#top)</sup>
>强烈推荐全部使用application/json

### <a name="h3.1">application/x-www-form-urlencoded</a><sup>[&#x2191;top](#top)</sup>

这是很多人误认为是HTTP的“默认”的数据类型。

在请求阶段，实际的表现是把表单的键值对拼接成key1=value1&key2[]=value21&key2[]=value22放在Body中发送。

它并不是什么HTTP的“默认”数据格式，而是早些年实现的浏览器自以为是的默认行为实现。这个默认行为也要看具体是什么样的表单项，如果是大文件上传，这个格式可能会变成multipart/form-data，并不是application/x-www-form-urlencoded。因此，事实上没有什么所谓HTTP的默认格式。

最新的javascript框架，以angular为例，已经不再把老式浏览器的默认MIME type作为自己默认的数据格式，而是转而使用application/json。

application/x-www-form-urlencoded由于受限于它自身的定义，尤其描述复杂的数据关系的时候是无能为力。

不管是服务端还是客户端，目前在技术上正确处理请求的MIME type，已经没有任何障碍。因此，我们更没有理由在RESTful API上使用表达能力非常有限的application/x-www-form-urlencode作为默认格式。


### <a name="h3.2">利用URL中的资源后缀（推荐）</a><sup>[&#x2191;top](#top)</sup>

```javascript
/posts/2341/comments/5543.json
/posts/2341/comments.xml?user_id=3244&rate=5
```

通过这种方式，可以在不依赖第三方工具的前提下，直接在浏览器中查看返回数据的不同格式。

HTTP头这样的信息是，要么通过浏览器插件，要么通过程序中才有办法定义，用户是没有其他途径。

如果既有的服务端的实现并不会解析URL中的.json、.xml等信息，那么也可以增量的方式对已有的API进行适配升级，对已经升级的API，用户就可以用这种方式很方便的查看到直观的数据结构。当然服务端是会有增加解析逻辑的代价，但是如果你的程序分层和抽象做的好，那么你可以用AOP的方式为你的API增加这种能力。


### <a name="h3.3">利用HTTP头Accept和Content-Type描述</a><sup>[&#x2191;top](#top)</sup>

| 类型 | HTTP头 | 说明 | 实例 |
| --- | --- | --- | --- |
| 请求 | Accept | 告知服务端自己能接收处理的格式。               | Accept: application/json,application/xml; |
| 响应 | Content-Type | 告知客户端自己返回的数据格式。同时需要定义字符集。 | application/json; charset=utf-8 |

常用[MIME types](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types)。

如果你的用户使用浏览器插件是完全没有问题的，那么与URL中的格式类型描述的手段相比，利用HTTP头的方式会更佳合乎逻辑，因为HTTP头里的Accept和Content-Type本来就是为这个目的专门定义的，显然它可以让你的URL更佳干净。


### <a name="h3.4">避免用无意义修饰信息包装实体对象</a><sup>[&#x2191;top](#top)</sup>

与在URL避免使用修饰语句的理由相同

```javascript
// Evil!
{
    "info": { // 这里info是废话
        "id": 134,
        "title": "Any useful infomations for title",
        ...
    },
}
// Evil!
{
    "data": { // 这里data是废话
        "id": 134,
        "title": "Any useful infomations for title",
        ...
    },
}
// Good!
{
    "id": 134,
    "title": "
    ...
}
```

有时，这种“信封信息”(Envelope)并不完全是“废话”，例如:
- 使用total_count, next_page等字段来为翻页提供必要的信息。
- 为jsonp形式的请求返回不同的callback语句。
```javascript
{
    "total_count": 123432, // 总帖子数
    "actual_count": 8, // 每页大小是20，但由于是最后一页，当前页只有8个帖子。
    "posts": [
        {
            "id": 134,
            "title": "Any useful infomations for title",
            ...
        },
        ...
    ],
    "callback": "renderPostListPage(posts, total_count, 344)",
    ...
}
```

但是，从目前发展来看，[CORS](http://www.w3.org/TR/cors/)或者[The Link Header Field(RFC5988)](http://tools.ietf.org/html/rfc5988#page-6)这种新标准的应用在绝大多数场景下都可以满足。因此，这种修饰方式封装实体数据的做法，应尽可能避免。

由于对服务没有配置权限，而很难进行跨域这种特殊情况特殊对待。但从职责来看API服务的问题，应优先在服务端得到解决。


### <a name="h3.5">命名风格</a><sup>[&#x2191;top](#top)</sup>

一般遵守客户端语言的命名规范。

例如:
- Javascript中通常使用camelCase。
- Python中通常使用snake_case。

>据统计snake_case风格比camelCase要容易阅读20%左右!


### <a name="h3.6">格式化和性能</a><sup>[&#x2191;top](#top)</sup>

> ?pretty=true

这里的格式化是指使用空白符缩进进行格式化的对人友好的数据展现。因此同样考虑对人友好，HTTP头等其他手段都会增加成本，只有URL的GET参数是最小成本的，同时实现它的代价也是微乎其微。

这种格式化由于包含了很多空白符，因此一定会增加HTTP数据包的大小。

通常对中等规模的数据来讲，这种格式化带来的大小上的差异在10%以内，而使用gzip压缩的情况下，可能连3%都不到。同时目前绝大多数应用中，响应数据是默认使用gzip进行压缩，而且这种压缩往往都是对客户端透明，更值得一提的是gzip压缩情况下甚至会带来60%以上的大小上的优化，压缩效果是非常显著的，因此格式化带来的大小增长，大部分情况下可以忽略不计。

如果你的场景需要用非常极限的压缩，换来更佳极致的性能，那么application/json格式本身就不是你的最佳选择，因为json毕竟是使用字符串类型表示所有数据，同时它还包含了很多《"》。这种情况下，你可能更愿意让服务端和客户端使用二进制的自定义的MIME type来进行数据交互。


## <a name="h4">状态反馈</a><sup>[&#x2191;top](#top)</sup>

### <a name="h4.1">状态码</a><sup>[&#x2191;top](#top)</sup>
HTTP状态码很多，可以看[List of HTTP status codes](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)了解一下。 

使用HTTP的状态码来描述状态，服务端和客户端都有代价。下方列表为几个候选状态码。

挑选个别几个在团队内部约定好即可，同时只靠HTTP的状态码是不足以描述清楚所有情况。

所有状态码都只有客户端针对不同的情况，能够做出不同的表现的时候才会有意义。

| Code | 默认返回文本 | 说明 |
| --- | --- | --- |
| 200 | OK | 一切正常。|
| 201 | Created | 与POST搭配使用。 |
| 204 | No Content | 与DELETE搭配使用。 |
| 304 | Not Modified | 对于调用频率非常高，应用了服务端缓存技术的API来讲使用要使用此状态码告知客户端使用的是缓存中的数据是必不可少的。 |
| 400 | Bad Request | 服务端要返回尽可能详细的错误反馈。见[下一节](#h4.2)。 |
| 401 | Unauthorized | 对需要身份验证的API必不可少。见[身份验证](TODO) |
| 403 | Forbidden | 服务端有能力处理请求，但是根据目前授权不允许响应操作。与401配套使用。 |
| 404 | Not found | 请求的资源不存在。非常实用。 |
| 405 | Method Not Allowed | 无法用请求的方法对目标资源进行操作。 |
| 410 | Gone | 废弃API可以用它告知客户端。 |
| 415 | Unsupported Media Type | API不支持处理该格式数据。例如向只支持json数据的API发送了xml格式的数据。 |
| 422 | Unprocessable Entity | 表单验证失败可以用这个状态码。例如客户端提交数据符合协议，但某些字段之间的逻辑关系可能有问题。  |
| 429 | Too Many Requests | 限制频率的API应该使用它。 |
| 500 | Internal Server Error | 任何一个API都应该避免让web服务返回这个状态码。不应让客户端能够感知到服务端配置错了或者写错代码。 |

### <a name="h4.2">异常详情</a><sup>[&#x2191;top](#top)</sup>

API为客户端反馈异常情况的时候，往往状态码和简单的一句描述，是远远不够的。

例如客户端提交注册表单，希望能够友好地提示用户哪些表单项填错了，是怎么错的。

又例如API是接收多个实体对象，服务端操作的时候检测到部分实体对象的操作由于某种原因无法完成，其他完好的实体对象又想允许完成本次操作。这种情况下也要告知客户端到底发生什么事情了，只有这样客户端才能够对用户反馈信息。

经在不同场景试用，如下这种方案比较实用。

```javascript
{
  // entities和errors对象作为可选项的原因是:
  // 大多数场景下判断返回数据中是否存在errors对象比其他形式更佳简单。
  // if (typeof response.erros !== 'undefined') {
  // ...
  // }
  // 而处理errors或者entities内部详情，总会是在先判断是否存在异常之后才会进一步细化处理。
  // 
  // 实体key。
  // 仅返回操作成功的实体对象。如果所有实体对象都没有被成功操作，则不返回这个对象。
  // 如果是单个实体操作就返回对象，如果是批量操作则返回数组。
  // 单个对象时，entity或object。
  // 多个对象时，entities或objects。
  // 根据团队实际情况协商约定，然后在所有API中始终如一地使用相同的key。
  "entities": [
    {
      "id": 3432,
      "title": "Any useful text",
    },
    ...
  ],
  // 异常详情key。
  // 仅在发生异常时返回，如果没有发生异常，则不返回这个对象。
  // 单个对象时，error。
  // 多个对象时，errors。
  "errors": {
    // code字段根据实际情况可以不使用。
    // 服务端想返回的消息内容和客户端希望使用的消息内容不一样的场景较多的时候，尽量使用。
    // 比如，如果msg内容足以帮助客户端为用户提供友好的反馈或者基于它直接进行国际化等处理，那么你可以不使用它。
    "code": 1024, 
    // 消息内容在任何时候都不应该省略，一是服务端从记录日志角度可能需要它，客户端也有可能直接使用它的内容。
    "msg": "Invalid characters in username",
    // 以上是异常情况的总体描述，除此之外，你往往还需要异常的详细信息来为表单项反馈异常情况。
    "detail": [
      {
        "field" : "username",
        "code" : 5432,
        "msg" : "用户名包含非法字段。"
      }, // 每个字段可能都有不同的提示，同时对同一个字段，不同的异常情况，可能会有不同的错误码和消息内容。
      ...
    ],
  }
}
```


## <a name="h5">数据操作</a><sup>[&#x2191;top](#top)</sup>

>数据操作可以抽象为最基本的5类：增、删、改、查、搜。


### <a name="h5.1">增</a><sup>[&#x2191;top](#top)</sup>

>POST

使用POST方法提交新的实体对象数据的时候，不应该改变已有数据。

相当于SQL中的INSERT。

最好返回新创建的实体对象。

>创建对象的时候由服务端产生的“创建时间、默认分类“等部分属性是客户端可能无法知道的。

成功时建议使用HTTP状态码：**201**


### <a name="h5.2">删</a><sup>[&#x2191;top](#top)</sup>

>DELETE

这个操作逻辑上也能知道只能成功一次。

对后续多次请求应返回“对象不存在”的状态。

成功时建议使用HTTP状态码：**204**


### <a name="h5.3">改</a><sup>[&#x2191;top](#top)</sup>

>PUT

PUT方法只会以幂等方式修改已有的数据。

相当于SQL中的UPDATE，HTTP Body相当于SET的参数，而URL相当于WHERE。

成功时建议使用HTTP状态码：**200**


### <a name="h5.4">查</a><sup>[&#x2191;top](#top)</sup>

>GET

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


### <a name="h5.5">搜</a><sup>[&#x2191;top](#top)</sup>
见[复杂操作](#h6)


## <a name="h6">复杂操作</a><sup>[&#x2191;top](#top)</sup>
>它们是RESTful API的设计难点

### <a name="h6.1">过滤</a><sup>[&#x2191;top](#top)</sup>

#### <a name="h6.1.1">利用URL</a><sup>[&#x2191;top](#top)</sup>

优点：直观，好理解。

缺点：需要服务端自行解析字符串。

| 符号 | 含义 |
| --- | --- |
| = | 等于 |
| != | 不等于 |
| > | 大于 |
| >= | 大于等于 |
| <= | 小于等于 |

```javascript
/notebooks?memory>=16g&os!=windows7
```

#### <a name="h6.1.2">利用POST的Body</a><sup>[&#x2191;top](#top)</sup>

优点：可以描述比较复杂的条件。
缺点：作为《查询》操作，与《新增》的操作含义冲突。

### <a name="h6.2">排序</a><sup>[&#x2191;top](#top)</sup>
>需要注意字段出现的顺序。

优点：直观，好理解。

缺点：需要约定排序字段，并且不能被字段命名冲突。

缺点：需要服务端自行解析字符串。

| 符号 | 含义 |
| --- | --- |
| 无或+  | 正排 |
| - | 倒排 |
| , | 用来分割多个排序字段 |

```javascript
/notebooks?sort=+brand,-memory,-insert_datetime
// 先按品牌名称字母顺序正排，然后根据内存大小倒排，然后再按数据插入顺序倒排。
```

### <a name="h6.3">字段挑选</a><sup>[&#x2191;top](#top)</sup>
>可以按需获取所需要的信息，减少通信数据的大小。

优点：直观，好理解。

缺点：需要约定排序字段，并且不能被字段命名冲突。

缺点：需要服务端自行解析字符串。

| 符号 | 含义 |
| --- | --- |
| fields | 挑选的字段列表 |
| , | 用来分割多个字段 |

```javascript
/notebooks?fields=id,brand,memory,price
```

### <a name="h6.4">翻页</a><sup>[&#x2191;top](#top)</sup>

#### <a name="h6.4.1">Request</a><sup>[&#x2191;top](#top)</sup>

##### <a name="h6.4.1.1">利用URL</a><sup>[&#x2191;top](#top)</sup>

优点：直观，好理解。

缺点：可能的命名冲突。

>对服务端友好

| 符号 | 含义 |
| --- | --- |
| offset | 数据偏移量。等于MySQL的LIMIT语句的第一个参数。 |
| count | 查询记录数。等于MySQL的LIMIT语句的第二个参数。 |

```javascript
/notebooks?offset=2400&count=20
```

>对客户端友好

| 符号 | 含义 |
| --- | --- |
| page | 当前页码。 |
| page_size | 每页记录数。 |

```javascript
/notebooks?page=24&page_size=20
```

##### <a name="h6.4.1.2">利用自定义Header</a><sup>[&#x2191;top](#top)</sup>

优点：URL干净。

缺点：客户端和服务端都需要自行处理Header。


#### <a name="h6.4.2">Response</a><sup>[&#x2191;top](#top)</sup>

>需要告知客户端总共还有多少数据，要不然页面上无法显示总页数。

##### <a name="h6.4.2.1">利用HTTP Header</a><sup>[&#x2191;top](#top)</sup>

优点：响应的Body内容干净。

缺点：需要自行处理Header。

| Header | 含义 | 实例 |
| --- | --- | --- |
| X-Total-Count | 总记录数。 | X-Total-Count: 3243 |
| Link | 见HATEOAS。 | Link: </cars?offset=15&limit=5>; rel="next",<br /></cars?offset=50&limit=3>; rel="last",<br /></cars?offset=0&limit=5>; rel="first",<br /></cars?offset=5&limit=5>; rel="prev"                             |

##### <a name="h6.4.2.2">利用HTTP Body</a><sup>[&#x2191;top](#top)</sup>

优点：不需要自行处理Header。

缺点：可能的命名冲突，结构也会多一层。

| 符号 | 含义 |
| --- | --- |
| total | 总记录数 |
| items或entities | 实体数据对象列表 |

```javascript
{
    total: 1343,
    items: [
        {
           ...
        },
        ...
    ],
}
```

## <a name="h6.5">其他</a><sup>[&#x2191;top](#top)</sup>

API设计过程当中，也会碰到一些无法用以上的操作无法描述的操作。

这类API通常都需要专门的使用说明。

例如：
- 激活操作。可以用PATCH提交{activated: false}。但往往可以通过把activated属性抽象成目标资源的属性的方式来解决。
- 收藏、取消收藏。可以在URL中把这种信息描述成子对象。Github是用“PUT /gists/:id/star”表示收藏，用“DELETE /gists/:id/star“表示取消收藏。
- 跨对象的搜索。关键字来进行搜索，但可能会匹配文章，可能会匹配评论，也有可能会匹配新闻公告等。这种情况下是没有办法事先明确资源。因此系统提供/search这种专用的URL是比较可取的。

## <a name="h7">版本化</a><sup>[&#x2191;top](#top)</sup>
>版本化会为你将来的重构、升级、并存等创造条件。

当然同时需要利用对域名的良好的命名为不同类型的资源划分好领域。

版本规范见[Semantic Versioning 2.0.0](http://semver.org/)，但除了MAJOR位，其他一般用不到。

API对任何应用，在分层角度上都是偏“底层”的“数据源”层。不大可能在双方没有预定的情况下随意增加删除字段。

但它们是也和任何一种程序一样，不可能永远不变。随着时间的推移，一定会需要升级更新。

版本化为API的平滑重构、流量控制、多版本适配提供很多便利。

版本信息按道理，理应出现在自定义Header中，但是这样一来你就没有办法用简单的GET方法（比如用浏览器）阅览多个版本API的响应数据了。

所以，
```javascript
/rest/v1/...
/restful/v2/...
/api/v3/...
/resources/v4/...
```

## <a name="h8">国际化</a><sup>[&#x2191;top](#top)</sup>

| 类型 | HTTP头 | 实例 |
| --- | --- | --- |
| 请求 | Accept-Language | en-US、en-UK、zh-CN、zh-TW、co-KR、co-JP... |
| 响应 | Content-Language | 略。参考请求。|

## <a name="h9">资源授权、身份验证、权限验证</a><sup>[&#x2191;top](#top)</sup>

### <a name="h9.1">OAuth 2.0</a><sup>[&#x2191;top](#top)</sup>

## <a name="h10">HATEOAS</a><sup>[&#x2191;top](#top)</sup>
>**H**ypermedia **a**s **t**he **E**ngine **o**f **A**pplication **S**tate

为客户端提供利用API进一步查找数据的附加信息。
在应用这项原则之前，需要评估一下API的客户端是否是在运行时刻决定跳转的目标地址。
绝大多数网页都不是运行时刻决定跳转地址，而是开发时刻由程序员写响应的跳转代码来决定的。因此我们的世界可能还没有准备好大面积使用HATEOAS原则。

```javascript
{
    ...
    "links": [
    {
        "rel": "self",
        "href": "/api/v1/drivers/23",
        ...
    },
    ...
}
```

## <a name="h11">穿过特殊的网络环境</a><sup>[&#x2191;top](#top)</sup>
>网络环境中有的代理服务可能只支持GET和POST方法

服务端最好利用自定义的HTTP Header来识别PUT、DELETE等请求。

| Header | 说明 |
| --- | --- |
| X-HTTP-Method-Override | PUT、DELETE、TRACE等... |






