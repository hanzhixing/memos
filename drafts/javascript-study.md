[返回首页](../README.md)

# Javascript备忘

# setTimeout

```javascript
var fn;

function say(message) {
    function output() {
        console.log(message);
    }

    fn = output; // 这里output的实际内存引用是闭包
}

say('hello');

setTimeout(fn, 1000);
```
