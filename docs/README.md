# fetch 获取/提交数据，以及开发环境下的数据 Mock

在 jQuery 开发时代，jQuery 已经为我们封装了非常优雅的 ajax 函数，并且针对各个浏览器都做了很好的兼容，非常棒。但是如果你用 React vue 或者 angular 去开发 webapp 时候，你还会再为了一个 ajax 去集成 jQuery 吗？这是一个问题。

另外一个问题，JavaScript 中的 ajax 很早之前就有一个诟病————复杂业务下的 callback 嵌套的问题。如果你对此了解不深刻，建议你去查一下“JavaScript promise”相关的资料。promise 正是 js 中解决这一问题的钥匙，并且作为标准在 ES6 中发布，接下来要介绍的 fetch 就用到了最新的 promise 

**[fetch](https://github.com/github/fetch)**就是一种可代替 ajax 获取/提交数据的技术，有些高级浏览器已经可以`window.fetch`使用了。相比于使用 jQuery.ajax 它轻量（只做这一件事），而且它原生支持 promise ，更加符合现在编程习惯。

## 安装

根据文档提示，用 npm 安装的话，执行`npm install whatwg-fetch --save`即可安装。为了兼容老版本浏览器，还需要安装`npm install es6-promise --save`。安装完成之后，注意看一下`package.json`中的变化。

## 基本使用

### get 的基本使用

参见`./app/fetch/test.js`源码，首先要引入依赖的插件

```js
import 'whatwg-fetch'
import 'es6-promise'
```

然后这样就可以发起一个 get 请求。这里的`fetch`是引用了插件之后即可用的方法，使用非常简单。方法的第一个参数是 url 第二个参数是配置信息。

```js
    var result = fetch('/api/1', {
        credentials: 'include',
        headers: {
            'Accept': 'application/json, text/plain, */*'
        }
    });
```

以上代码的配置中，`credentials: 'include'`表示跨域请求是可以带cookie（fetch 跨域请求时默认不会带 cookie，需要时得手动指定 credentials: 'include'。这和 XHR 的 withCredentials 一样），`headers`可以设置 http 请求的头部信息。

接着说。

fetch 方法请求数据，返回的是一个 Promise 对象，接下来我们就可以这样用了——完全使用Promise的语法结构。

```js
    result.then(res => {
        return res.text()
    }).then(text => {
        console.log(text)
    })
```

或者这样用

```js
    result.then(res => {
        return res.json()
    }).then(json => {
        console.log(json)
    })
```

注意，以上两个用法中，只有`res.text()`和`res.json()`这里不一样————这两个方法就是将返回的 Response 数据转换成字符串或者JSON格式，这也是 js 中常用的两种格式。


### post 的基本使用

参见`./app/fetch/test.js`源码，首先要引入依赖的插件

```js
import 'whatwg-fetch'
import 'es6-promise'
```

然后用 fetch 发起一个 post 请求（有`method: 'POST'`），第一个参数是 url，第二个参数是配置信息。注意下面配置信息中的`headers`和`body`的格式。

```js
    var result = fetch('/api/post', {
        method: 'POST',
        credentials: 'include',
        headers: {
            'Accept': 'application/json, text/plain, */*',
            'Content-Type': 'application/x-www-form-urlencoded'
        },
        // 注意 post 时候参数的形式
        body: "a=100&b=200"
    });
```

fetch 提交数据之后，返回的结果也是一个 Promise 对象，跟 get 方法一样，因此处理方式也一样，上面刚描述了，因此不再赘述。


## 抽象`get`和`post`

如果每次获取数据，都向上面一样写好多代码，就太冗余了，我们这里将 get 和 post 两个方法单独抽象出来。参见`./app/fetch/get.js`和`./app/fetch/post.js`的源码。

需要注意的是，`post.js`中，将参数做了处理。因为上面的代码中提到，`body: "a=100&b=200"`这种参数格式是有要求的，而我们平时在 js 中使用最多的是 JSON 格式的数据，因此需要转换一下，让它更加易用。

这两个方法抽象之后，接下来我们再用，就变得相当简单了。参见 `./app/fetch/data.js`

```js
    // '/api/1' 获取字符串
    var result = get('/api/1')

    result.then(res => {
        return res.text()
    }).then(text => {
        console.log(text)
    })
```


