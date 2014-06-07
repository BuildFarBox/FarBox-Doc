url: template-syntax
Title: 模板语法
toc: yes

> FarBox的基本模板引擎是建立在HTML基础上的[Jinja2][jinja]框架。
> 但你可以同时使用Jinja2（.html文件后缀）与Jade(.jade文件后缀)语法。

## Jinja2语法

### 嵌入变量

FarBox的模板语法就是普通的HTML，但可以使用`{{}}` `{%%}`嵌入特殊的API代码。

```
{{ 使用变量或者运行函数 }}
{% 逻辑运算 %}
```

### 逻辑代码

如果循环、判断，则会有对应起止，示例如下：
```
{% if site.title %}我有名字{% endif %}  --> 如果没有 {% endif %} 会报错

{%for post in posts %}
    文章的标题: {{post.title}}
{% endfor %} --> 如果没有{% endfor %} 也会报错
```

除了{% if %}、{% endif %}、{% for %}、{% endfor %}这些基本逻辑运算外，这几个也是常见的：{% block %}、{% endblock %}、{% extends 'something.html' %}、{% include 'something.html' %} 实现模板之间的继承、引用。

## 模板结构

### 模板的继承(extends)

父模板(比如名为base.html)，它负责的是一个基本的框架，
```
Title Here
{% block head %}{% endblock %} 
{% block content %}{% endblock %}
```

子模板(比如名为detail.html)，它继承父模板的框架，并最终渲染页面
```
{% extends "base.html" %}  # 这里申明从哪个父模板继承
{% block content %}  # 声明block
    具体的内容
{% endblock %}
    **没有在block里面的内容，最终不会呈现，比如我这一行文字。**

{% block abc %}
    在base.html中，并没有声明一个abc的block，所以，这段文字也是不会被呈现。
{% endblock %}
```

使用Jade的语法可以写成
```jade
extends base
block content
    具体的内容
**没有在block里面的内容，最终不会呈现，比如我这一行文字。**        
block abc
    在base.html中，并没有声明一个abc的block，所以，这段文字也是不会被呈现。
```

### 模板的引用(include)

比如名为`t1.html`的源码如下：
```
1234567
{% include 't2.html' %}
89
```

而`t2.html`的源码如下：
```
我是嵌入的文字或者代码
```

那么最终呈现为：
```
1234567
我是嵌入的文字或者代码
89
```

### 模板继承、引用的路径

比如有以下的目录结构（**注意实际使用过程中，不要出现以中文为名的文件（或文件夹）**）
```
|--FarBox
    |-- template(目录)
        |-- base.html
        |-- index.html
        |-- post.html
        |-- includes(目录)
            -- comments.html
        |-- sub_folder(目录）
            -- sub_index.html
```

不论在sub_index.html或者index.html申明继承base.html，都应该是`{% extends 'base.html'%}`.

而`comment.html`如果想要被引用的话，则要是这样的路径`{% include 'includes/comments.html' %}`

简单的说，继承、引用的模板路径是针对于`template`这个文件而言的相对路径。

`sub_folder.sub_index`是等效于`sub_folder/sub_index.html`的，也同样等效于`sub_folder/sub_index.jade`; `base`则等效于`base.html`和`base.jade`。

#### FarBox实际模板路径

`sub_folder/a_template.html` `sub_folder/a_template.jade`, 作为核心模板，在FarBox的系统中，实际上会被转译为`.sub_folder.a_template`, 所以两者是完全等价的。




## Jade语法

FarBox将我们常见的文件目录结构转化为了可快速查询的数据库调用，而FarBox API的主要功能在于渲染页面（HTML）。

如果用传统的角度看待，那么，一个是`后端`，一个是`前端`。所以，我们一直在寻找可以融合前后端的API语法，经过改造的`Jade`语法，基本上满足了我们的设计需求。

[Jade](http://jade-lang.com/)是Node.js世界中的一种Python式缩进的模板语法，经过改造后，FarBox可以将`.jade`文件转化为`Jinja2`的语法。

为了更好地进行编写，你可能需要为自己的代码编辑器/IDE增加`Jade`语法的支持插件。


### HTML标签、id、class

```jade
.class_name this is content
// 默认会创建div标签，最终结果为<div class="class_name">this is content</div>

tag_name.class_name
// 最终结果为<tag_name class="class_name"></tag_name>

p.class1.class2.class3
// 多个class，最终结果为<p class="class1 class2 class3"></p>

a.a_class(href="#", title="this is a link")
// 多个属性，用`,`隔开，最终结果为<a class="a_class" href="#" title="this is a link"></a>

span#dom_id.class_name_1.class_name_2 this is content
// 最终结果为<span id="dom_id", class="class_name_1 class_name_2">this is content</span>

.class_name this is line 1
| this is line 2
//
	 | 可以在跨行中使用，最终结果为<div class="class_name">
		this is line 1
		this is line 2
	</div>

```

### 变量与HTML标签

如果直接将具体的变量与HTML标签关联起来，可参照如下语法。

```jade
node post.content  
//等同于<node>post.content</node>

var = post.content
// 等同于 {%set var = post.content %}, 是一个变量的赋值

node= post.content 
// =号左前无空格，则是将变量转为HTML标签，等同于<node>{{ post.content }}</node>

node= 'post.content'
// 等同于<node>post.content</node>

```

如果HTML标签中有其它属性定义的。

```jade
a(href=post.url)= post.title
// 等同于 <a href={{post.url}}>{{post.title}}</a>

a(href='/post/{{post.url_path}}')= post.title
// 这混合了Jinja2语法，等同于 <a href='/post/{{post.url_path}}'>{{post.title}}</a>
```


### for/if

```jade
head
	if site.title == 'test'
		title= site.title
	elif site.title == 'a test too'
		title= site.title + '!!!'
	else
		title= 'blank'
	
body
	for post in posts
		h1= post.title
```

等效于
```html
<head>
	{% if site.title=='test' %}
	    	<title>{{ site.title }}</title>
	{% elif site.title =='a test too' %}
		<title>{{ site.title + '!!!' }}</title>
	{% else %}
		<title>blank</title>
	{% endif %}
</head>
<body>
	{% for post in posts %}
		<h1>{{ post.title }}</h1>
	{% endfor %}
</body>
```

#### 注意！！
不要将if/else/for跟其它逻辑混合, 以下语法都是错误的，会导致模板编译无法成功进行。
```jade
if post
    post.content
else no content 
// 这样写的else是无效的，既不是HTML标签，也不是逻辑判断； 要空一行先。
```

### 运行函数

```jade
+raise_404()
// 前面增加+号，则不会被转译为HTML的标签，最终的结果为{{ raise_404() }}
// 这只是举个例子，raise_404是一个特殊函数，实际上，不加+号也无妨;
// 但其它普通函数或者你自己定义的函数，则必须使用+号来声明。
```

## Jade的继承与引用

### extends与include

这分别对应Jinja2语法中的`extends`与`include`，但跟Jinja2不一样的是，不要增加单引号/或双引号。

```jade
extends base
extends base.jade
// 等价于 {% extends 'base.jade' %}

include include/comments
include include/comments.jade
include include.comments.jade
//
	等价于{% include 'include/comments.jade' %}
	我们可以使用`.`代替`/`，为避免冲突，必须保证有文件后缀名，比如 include include.comments 是错误的语法
```

### macro/mixin

`macro` 是Jinja2的概念，`mixin`则是Jade的概念; 在FarBox API中两者基本等价。

`macro`相当于自定义一个函数，然后在模板中进行调用，另外，我们也支持对这个函数产生的数据进行缓存，前提是使用`cache_`为开头的函数名。

Jinja2是Python语言创建的模板框架，所以`macro`的定义方式，基本接近Python语言中函数的定义。

```jade
mixin sidebar(name)
	h1= name
	p this is sidebar

// 等价于
{% macro sidebar(name) %}
	<h1>{{ name }}</h1>
	<p> this is sidebar </p>
{% endmacro %}
```

如果macro的定义与调用，都在同一个模板页，则后面可以直接使用这个定义出来的函数。

如果处于不同模板页面，则要先行import；这是`Jade`语法中不存在的概念，所以要使用Jinja2语法。

比如

```jinjia2
{% from "mixins.jade" import get_url %}

{# 注意`mixins.jade`需要使用双引号或者单引号包围 #}
```

**macro相对于include而言，它的性能其实更高，include不可避免地会带入一个变量空间，从而造成混乱的概率增加；而macro的内容如果不调用外部变量，则可以有效地防止变量空间混乱的情况。**



## Jade与Jinja2语法融合

### 常见例子

在.jade的模板文件中，可以在单独一行中使用Jinja2的语法。

```jade
extends base
{% from "mixins" import make_posts_list %}
{{ site.title }}

.entry
	| {{ post.title }} -
	| {{ post.date }}

```


{# 以上这样的写法是能够正式解析的 #}

### 变量关键词

'post', 'posts', 'site', 'sites', 'files', 'tags', 'tag', 'folder', 'folders','category', 'images', 'albums', 'album', 'next_one', 'pre_one', 'paginator', 'pager'，这些作为变量的关键词，不能直接作为HTML的Tag处理。

举例说明

```jade
/*这样是有效的，会解析为<div>{{post.content}}</div>*/
div
	post.content


/*doc不是被保护的关键词，会被解析为<div><doc class="content"></doc></div>*/
div
	doc.content

/* 如果需要doc也能同post变量一样工作，则要写成*/
div= doc.content
/* 或者 */
div
	| {{ doc.content}}

```

### 函数关键词

'load', 'set_content_type', 'redirect', 'a_with_selected', 'set_per_page', 'add_doc_actions' 这些FarBox系统函数，则可以在单行中直接使用，不会被Jade特别转译为HTML代码。

'need_login', 'need_admin', 'raise_404' 这些函数在Jade语法中，不论是`need_login()` 还是`need_login`这样的写法，最终都会被解析为`{{ need_login() }}`。


## Jade的自动补全

> FarBox对.jade后缀的模板文件进行编译过程中，会进行以下的自动补全：

- 如果没有声明doctype，则默认补全为`doctype html`
- 如果是html类型的doctype，但没有申明feed地址，默认会补全feed标签，其地址对应`/feed`
- 如果是html类型的doctype，但没有声明charset的，默认补全为`utf8`


[jade]: http://jade-lang.com
[jinja]: http://jinja.pocoo.org/docs/
[jinja-cn]: http://docs.torriacg.org/docs/jinja2/templates.html


