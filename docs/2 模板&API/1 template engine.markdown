Title: 模板引擎与URL匹配
url: template-engine
toc: yes

> FarBox的基本模板引擎是建立在HTML基础上的[Jinja2][jinja]框架。

## 基本语法

### Jinja2语法

FarBox的模板语法就是普通的HTML，但可以使用`{{}}` `{%%}`嵌入特殊的API代码。

```
{{ 使用变量或者运行函数 }}
{% 逻辑运算 %}
```

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


## URL与模板的匹配规则
网站所有的URL都是通过模板的文件路径决定的。
假设某个网站的模板文件路径为`template/post.html`, 默认对应的是`http://domain.com/post`。


### URL匹配的规则

> 下面为了方便说明，模板文件名的后缀为`.html`, 实际上 `.jade`的文件后缀效果是等同的。
> `/template/folder/index.html`的效果与`/template/folder.html`等同。

如果URL是以`.txt` `.mk`  `.md` `.makrodnw`结尾的，则系统会先尝试去寻找对应路径的文本文档，并用`template/markdown.html`进行渲染；如果这个模板不存在，则继续使用正常的路由逻辑。

1. step1, 访问网站首页时, 匹配index.html模板.
- step2, 完整匹配URL, 比如访问`/path/sub_path`匹配`/path/sub_path.html`.
- step3, 仅匹配URL的第一部分, 比如访问`/first_path/sub_path`, 但是`/first_path/sub_path.html`不存在,则会尝试匹配`first_path.html`.
- step4, 如果前三次都无法匹配，则会尝试匹配`I-Love-FarBox.html`(*慎用，黑魔法专用*).
- step5, 以上匹配都失败后,则使用`404.html`作为`Not Found`的页面。

**注: `URL的第一部分`, 默认是指第一个`/`之前的, 如果URL中存在`~`, 则取`~`前的字符作为第一部分.**
```
/path/sub_path 的第一部分是 `path`
/path/sub_path/~hello/world 的第一部分则是`path/sub_path`
```

### 混合路径的URL

如果模板文件处于`template`目录内，那么文件名中有`+`连接多个关键词，可以实现一个页面匹配多个URL的效果。

比如`template/index+tags+archive.jade`或`template/index+tags+archive.html`能同时处理`首页`、`/tags`、`/archive`这三个URL。


### 注意事项
1. 文件路径中不要出现`.` `#`以及非英文字符.
- 不要试图使用index.html完成整个网站的route匹配
- `/t/*`是系统URL，映射到`/template/`；所有`/template/`目录下的静态资源，使用`/template/<path>`来获取。


[jade]: http://jade-lang.com
[jinja]: http://jinja.pocoo.org/docs/
[jinja-cn]: http://docs.torriacg.org/docs/jinja2/templates.html
