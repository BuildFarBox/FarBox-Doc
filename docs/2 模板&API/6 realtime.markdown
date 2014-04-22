Title: 实时接口
URL： realtime-api
Date: 2014-04-15 17:29:27

## 实时接口概述

实时接口需要通过Websocket的连接，Chrome、FireFox、Safari、IE>10 .etc 这些浏览器原生支持。

> 实时接口，可以让你的网站发挥更大的想象力！

FarBox实时接口的一些注意项:
1. 如果已经连接服务器，但是60分钟内没有任何数据的传输，端口会自动关闭；
2. 一个连接，最长时间不能超过24个小时；
3. 避免无意义地频繁建立连接，容易被服务器视为攻击行为，而屏蔽当前访客的IP。


## 文件变化通知接口

> 当你网站目录内的文件发生变化时，当前网站访客的浏览器Websockt会收到一条通知信息。
> FarBox仅会推送这个事件，如果要获取文件的详细数据，需要自己定义模板API来实现。

接口URL: `ws://realtime.farbox.com/notes`； 如果当前为`HTTPS`链接，请用`wws:` 代替`ws:`。

注意：
- 一个文件的变动，对应一条通知信息。
- 必须在当前网站内使用这个接口，反之，是无法获取数据推送的。


### 代码示例
CoffeeScipt为例，其中message.data返回的是一个JSON化的字符串。
```coffeescript
if WebSocket? and JSON?
    if document.location.protocol == 'https:' then ws_protocl='wss:' else ws_protocl='ws:'
    ws_url = ws_protocl+'realtime.farbox.com/notes'
    socket = new WebSocket(ws_url)
    socket.onmessage = (message)->
        note = JSON.parse(message.data)
```

### 对应的字段

| 字段 | 说明 |
| --- | --- |
| site_id | 站点id |
| path | 变动文件的路径（相对于当前站点目录） |
| doc_type| 比如 post/image/template，即文档类型 |
| is_deleted | 文件是否被删除 |
| date | 事件发生时间 |



## AutoReload模式

`AutoReload`模式是指当网站的源文件(html/jade/css/scss/js/les)发生变化的时候，网页会自动进行页面的重新渲染或者刷新。

这是对开发者极其友好的特性，你并不需要第三方的工具做额外的支持, 就可达到代码实时可视的效果。

你也可以修改模板源代码，来替换`/template/js/autoreload.js`，从而实现自己的AutoReload功能。

关于autoreload的站点设置项，请参考[此处](//doc.farbox.com/read/site-configs#toc_22)。






