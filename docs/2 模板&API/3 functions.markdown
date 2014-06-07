Title: 函数与数据处理
Position: 3.9
url: functions
toc: yes

> 函数的调用通过{{ function_name(v1, v2, v3=something, .etc) }}的语法，有些函数并不会返回值。
> **函数是晚于默认变量运行的，除非默认变量被嵌套在macro内，才会打破这个优先级。**

## 文档处理

### has
判断数据、文件有无的一个函数, 接受一个参数。
1. has('posts') 网站中有无日志
2. has('images') 网站中有无图片
3. has('categories') 网站目录的1级子目录中有无日志的分类目录
4. has(<doc_path>), 比如has('about.md')有无具体路径的文件

### add_doc_actions
这里的doc是指post/image/file类型的数据对象。
执行这个函数并不会返回任何值，但可以让doc对象获取**访问数统计**、评论等功能。
```
{% set post=get_doc(type='post', path='this-is-article.txt') %}
{{ add_doc_actions(post) }}
```
那么当前URL被访问的时候，并且加上后面的`action=xxx`则会有特定的数据返回。（如果是日志，那么需要其status=='public'才会对以下几个action生效）

1. `URL?action=show_raw`， 如果是post类型的，会返回文章的原始文字。
- `URL?action=show_json`, 如果是post类型的，会返回JSON格式的post数据。
- `URL?action=edit`, 如果是post类型，会跳转到Web版的编辑其中。

另外，通过`POST`的方式请求当前URL，则可以对该文档进行评论。

**使用这个函数后，对应的doc文档在被访问的时候，其`visits`这个属性会+1.**

### 添加评论
首先你需要使用add_doc_action(doc)这个函数，那么doc这个数据就将被允许评论。评论的数据会同步回用户的Dropbox内。

在页面中，需要通过POST的方式进行数据的传送。URL地址即文档的地址（比如一篇日志的详细页）。

| 字段 | 说明 |
| --- | --- |
| author | 评论的作者名 |
| email | 评论作者的邮箱 |
| site | 评论者留下的邮箱 |
| json | 如果为true, 以JSON形式返回单个评论的数据 |

**在同一篇文档的评论中可以@someone，如果对应的someone（作者昵称）如果有邮箱留下来的话，则对方会收到邮件通知。**

### put

接受3个参数，一个为path, 一个为content，以及from_url(表示从哪个地址下载，默认为None)。

`put('hello.txt', 'this is content')` 表示文件名为`hello.txt`，内容为`this is content`会增加，并重新推送回Dropbox端。

注意事项：
1. 一个页面内，最多运行2个put函数；
2. 5分钟内调用不能超过1,000次。
3. 谨慎使用from_url，如果被系统侦测为恶意使用，当前账户会被直接封禁。

### append

可以将一段文本增加到某个文本文件中，接受以下参数：

- path: 文件路径(必须)
- content: 需要增加的文字内容 (必须)
- lines: 默认为1，最大值为10，表示与原来内容的间隔行
- check: 默认为False，如果为True，表示最后一次append的内容被重复，则直接忽略
- reverse: 默认为False，表示插入文件尾部；如果为True，则插入头部

## 开发者相关

> 开发者相关的函数，本身的权限比较大，后续只有特定的账户类型才能进行调用。


### get_hash

接受任意参数，其中`hashlib`(默认为md5)与`sort`(默认为True)为特殊参数，前者表示散列的方式，支持md5或者sha1，后者表示是否对传入的参数进行排序。

代码示例(微信公众平台的验证):

```jade
rv = request.values
if rv.signature != get_hash('<your token>', rv.nonce, rv.timestamp, hashlib='sha1')
    +raise_404()
```

### get_outbound_link_password

可以获取外链图片的密码，并且组装URL，做为`outbound_link_password`的GET参数。

get_outbound_link_password接受一个参数`days`，表示外链密码的有效期。默认为3天，days的取值范围为1-60（天）。


### get_var/set_var
可以存储变量到当前网站，以实现一些特殊的定制。需要注意，这些变量并不会同步到Dropbox的存储中。

key与value不能过长（64字节内），变量总数不能超过100.如果value是数值，那么其应该是介于正负4294967296之间的。

通过set_var('test', 1)， 再get_var('test')得到的就是1了。

### send_request

可以发送外部请求到其它网站。

接受以下参数：

- url: 外部请求的URL
- method: 默认为GET，也可以是POST
- headers： 默认为`{}`, 可以指定特殊的头部信息
- 其它k/v： 比如`send_request(<url>, k1=v2, k2=v2)`, 后面可以跟任意k/v类型参数，如果GET则是自动补足倒URL中，如果是POST，则是作为data发送。

send_request会返回一个response对象，`response=send_request('http://google.com/')`， response则有以下字段：
- text: 返回的数据，文本格式（如有必要，重新编码为utf8或unicode）。
- status_code: HTTP状态码
- cookies: resposne被目标URL写入的Cookie
- headers: 返回的页面头部信息
- json: 如果是json格式的话，可以直接调用
- ok: 当前结果（即response））是否正常


注意事项:

1. 如果被服务器认为属于外部攻击行为，则会被封禁当前账户。
2. 不可用来进行proxy，特别是被GFW封禁的源站。
3. 一个页面内最多不超过3次调用；5分钟内，最多不超过200次调用。
4. 不要请求大尺寸文件（比如一张图片），页面首先会超时，其次会被认定一次危险行为。

### timestamp_diff

`timestamp_diff(<a unix timestamp>)`， 可以返回当前时间与指定时间戳的相差秒数。

### cronjob

接受一个参数hours，表示cronjob每隔几个小时运行，最小值为1，最大值为30.

如果一个页面中使用了cronjob这个函数，那么这个页面在第一次被访问（或者当前站长访问时）会被激活，之后再访问则会抛出400错误，而转为后台定时运行。




## 页面渲染相关的函数


### load

这是一个快捷方式，可以载入多个CSS/JS文件，用空格隔开，比如`load("/hello.css /word.js")`会转化成以下HTML代码:

```html
<link  type="text/css" rel="stylesheet" href="/hello.css"/>
<script type="text/javascript" src="/world.js"></script>
```

我们内置了以下快捷的资源：

| name | Lib | Version |
| ----- | ---- | ---- |
| jquery | Jquery | 2.0.3 |
| font | Font Awesome | 4.0.3 |
| fonts | Font Awesome | 4.0.3 |
| mathjax | MathJax | 2.3 |
| pure | Pure | 0.3.0 |
| essage | Essage | - |

比如 `load("jquery")`就会自动载入Jquery 2.0.3的版本。

#### 调用方法
> load是懒人的用法，支持以下这些写法；万一最终解析的结果不正确，请使用原生的HTML写法。

- load(`<path or shortcut>`)
- load(`<path or shortcut>`, version)
- load('`<shortcut1>#version <shortcut2>#version`')
- load(`<prefix_path>`, [sub_filename, sub_filename2])
- load( [`<path or shortcut>`, `<path or shortcut 2>`] )
- load('`<path or shortcut>` `<path or shortcut 2>`')
- load('`<path or shortcut>`, `<path or shortcut 2>`')
- load('`<path or shortcut>` `<path or shortcut 2>`, `<path or shortcut 3>`')




### get_request_path
可以获得当前URL偏移的路径，接受一个参数offset，默认值为1。
假设当前访问`/requested/path/sub_path`,那么：
1, get_request_path(offset=1) --> `path/sub_path`
2, get_request_path(offset=2) --> `sub_path`

但路径中有`~`符的会特殊处理，永远都会以第一个`~`作为计算offset的起点。
比如访问`/requested/path/~sub_path/sub_sub_path`, 那么:
1， get_request_path(offset=1) --> `sub_path/sub_sub_path`
2, get_request_path(offset=2) --> `sub_sub_path`

这个函数通常会和[get_data](get-data)配合使用，以获取通过URL传递的数据对象。

### set_content_type
这个函数可以更改页面输出的类型，在输出JSON、XML等特殊类型页面的时候会有作用，这个函数本身不返回任何值。

比如JSON的页面`{{ set_content_type('application/json') }}`

### set_no_inject
表示页面中不嵌入任何可能的代码（比如数学公式脚本、AutoReload脚本）。


### redirect
接受一个参数`url`，比如`{{ redirect('http://google.com')}}`, 可以强制页面跳转到Google的主页。


### raise_404
如果使用，将会触发404页面。不接受任何参数，调用方法为`{{ raise_404() }}`

## 权限相关

### need_login
如果当前访客没有登录，会进行登录的跳转；反之则不返回值。

### need_admin
1. 如果当前访客没有登录，则进行登录的跳转；
2. 如果已经登录，但不是网站的所有者，那么会提示出错；
3. 如果已经登录，并且是网站的所有者，不返回任何值。

## HTML生成函数

###  a_with_selected
会生成一个A标签的HTML代码片段，如复合匹配，它会有一个`selected`的class；接受参数(url, title, dom_id=None)。
- url, 也就是href的值
- title, 是包裹在A标签中的字符
- dom_id, 如果有声明，则会是A标签的id值

自动生成一个A标签的HTML源码。
```
{{ a_with_selected('/hello', 'Hello, I am Title') }}
```
上面这段代码一般情况下，会生成`<a href="/hello">Hello, I am Title</a>`
在`equal=False`的情况下，如果当前访问的URL为`/hello`，则会生成`<a href="/hello" class="selected">Hello</a>`.


### bread_nav

可以讲一个指定的path字符串，转为一段面包屑导航的HTML代码，接受参数(path, prefix='', prefix_name='/', split_by='/')。

- path，指完整的路径
- prefix，表示导航中所有超级链接的前缀
- prefix_name表示第一级时候显示的文本
- split_by表示各部分连接符。

假设`<path>` 为 `/folder 22/img_3851.jpg`； 
那么 `{{ bread_nav(path,'/folder/', prefix_name='Albums>'), split_by='>' }}` 最终转化为一下HTML代码:
```html
<ul class="bread_nav">
 	<li>
 		<a href="/folder/">Albums></a>
 	</li>
	<li>
		<a href="/folder/folder 22/">folder 22></a>
	</li>
	<li>img_3851.jpg</li>
</ul>
```




