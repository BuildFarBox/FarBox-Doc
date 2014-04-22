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


## 页面渲染相关的函数

### SCSS/SASS/LESS

FarBox支持直接将SASS/SCSS渲染为css文件。

比如`template/styles.scss`，那么访问`http://yoursite.com/template/style.scss?format=raw`的时候，才会保留源格式；其它都是自动编译为CSS进行呈现。

对LESS格式的文件也是同样支持的，但是除了基本的层级关系、变量应用外，对LESS的支持相对有限，比如对应extend的写法并不支持。

另外需要注意的是，编译的过程是忽略所有include/import类似的引用逻辑。

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


### set_per_page

可以设置当前分页时默认的每页条目录。
但需要注意的是，`默认变量`的优先级，永远高于函数，

```jade
// jade语法
+set_per_page(10)
for post in posts
	h1= post.title
// 此时posts是每3条进行分页，无视10这个参数。`posts`这个变量已经先行获取了

//变通的办法, 将默认变量用一个函数包裹
// mixin是Jade中的语法，相当于Jinja2中的macro，即一个构造函数。
mixin make_list()
	for post in posts
		h1= post.title
+set_per_page(10)
+make_list()
```

### make_site_live
不接受参数，不返回值。如果处于网站所有者登录的状态下，会将网站设置为live模式，适用于`编写模板代码`的场景。

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




