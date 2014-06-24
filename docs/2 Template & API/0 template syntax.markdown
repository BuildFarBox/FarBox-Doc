url: template-syntax
Title: Template Syntax
toc: yes

> FarBox's basic template engine is base on the [Jinja2][jinja], which is a template frame base on HTML.
> But you can use both Jinja2(file suffix is .html) and Jade(file suffix is .jade).


## Jinja2 Syntax

### Basic Syntax

The Jinja2 syntax is similar to HTML, but you can use `{{}}` `{%%}` to embed some API code.

```
{{ a variable or run a function }}
{% logical and other operations %}
```

### Logical Code

If there is a loop or a judgement, you need start-end tag pairs, like this：
```
{% if site.title %} My Name {% endif %}  --> no {% endif %} here will raise an error.

{%for post in posts %}
    Post Title: {{post.title}}
{% endfor %} --> no {% endfor %} here will raise an error.
```

Besides `{% if %}`、`{% endif %}`、`{% for %}`、`{% endfor %}`, there are some others like：`{% block %}`/`{% endblock %}`/`{% extends 'something.html' %}`/`{% include 'something.html' %}`.

## Template Structure

### Template Inheritance(extends)

A parent template file (like base.html), usually means a basic frame.
```
Title Here
{% block head %}{% endblock %} 
{% block content %}{% endblock %}
```

A child template file (like detail.html), inherits from the parent template,
```
{% extends "base.html" %}  # inherits from which parent template
{% block content %}  # declare a block
    detail contents
{% endblock %}
    ** this line is not in a block, so it won't be displayed **

{% block abc %}
    there is no block named `abc` in base.html, so this line won't be displayed either.
{% endblock %}
```

Rewrite in Jade syntax:
```jade
extends base
block content
    detail contents
** this line is not in a block, so it won't be displayed **      
block abc
    there is no block named `abc` in base.html, so this line won't be displayed too.
```


### Include a Template

For example, there is a template file named `t1.html`：
```
1234567
{% include 't2.html' %}
89
```

and `t2.html`：
```
I am a included text or code.
```

The last result：
```
1234567
I am a included text or code.
89
```


### Template File Path

Assume your site folder looks like below:
```
|--FarBox
    |-- template(a directory)
        |-- base.html
        |-- index.html
        |-- post.html
        |-- includes(a directory)
            -- comments.html
        |-- sub_folder(a directory）
            -- sub_index.html
```

If `sub_index.html` or `index.html` wants to use base.html, the path must be `base.html`, like `{% extends 'base.html'%}`.

And if you want to include `comment.html`, the path in the code likes this `{% include 'includes/comments.html' %}`

In brief, the template path for `include` `extends`, must be relative to your `template directory`.

And `sub_folder.sub_index` is equal to `sub_folder/sub_index.html` and `sub_folder/sub_index.jade`; `base` is equal to `base.html` and `base.jade`.

#### The Compiled Template Path in FarBox

`sub_folder/a_template.html` and `sub_folder/a_template.jade`, will treated as the same template files. Both of them are the core template file, in fact, they will be compiled to a file named `.sub_folder.a_template`.


## Jade Syntax

FarBox turns file-folder base on OS file system into a query-able database, then the FarBox API queries the database to get data to render the pages(HTML).

In a traditional point of view， database means `backend`, and the pages mean `frontend`. And we found the Jade syntax which can help us to mix backend and frontend together, coding less but more beautiful. We customed Jade syntax for FarBox, it works well and becomes more human!

[Jade](http://jade-lang.com/) is a pythonic template frame from the world of Node.js. In fact, FarBox will compile a `.jade` file into a file follows `Jinja2` syntax on server side.

### HTML tags、id、class

```jade
.class_name this is content
// result:<div class="class_name">this is content</div>

tag_name.class_name
// result:<tag_name class="class_name"></tag_name>

p.class1.class2.class3
// multi-classes，result: <p class="class1 class2 class3"></p>

a.a_class(href="#", title="this is a link")
// multi-properties, split by `,` , result: <a class="a_class" href="#" title="this is a link"></a>


span#dom_id.class_name_1.class_name_2 this is content
//result: <span id="dom_id", class="class_name_1 class_name_2">this is content</span>

.class_name this is line 1
| this is line 2
//
	 | is used in multi-lines, the last result: 
 <div class="class_name">
		this is line 1
		this is line 2
	</div>
```


### Variables and HTML Tags

If you want to relate a variable to a HTML tag:

```jade
node post.content  
//it's equal to <node>post.content</node>

var = post.content
// equal to {%set var = post.content %}, set a value to a parameter

node= post.content 
// before `=` there is no blank space, equal to <node>{{ post.content }}</node>

node= 'post.content'
// euqal to <node>post.content</node>

```


If there are extral properties forthe HTML tag:
```jade
a(href=post.url)= post.title
// euqal to <a href={{post.url}}>{{post.title}}</a>

a(href='/post/{{post.url_path}}')= post.title
// this syntax mixed Jinja2 and Jade, equal to <a href='/post/{{post.url_path}}'>{{post.title}}</a>
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

equal to
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

#### Notice！！
Do no mix `if/else/for` into other logics, the syntax below is error.
```jade
if post
    post.content
else no content 
// the else is not valid, it's not a HTML tag, neither a logical judgement. `else` should in a seperated line.
```

### Run Functions

```jade
+raise_404()
// add `+` before the function name, equal to: {{ raise_404() }}
// This is just an example，in fact raise_404 is a speical function, event has no `+`, it works well.
// But other common functions, must need a `+` to declare.
```


## Inheritance in Jade

### extends & include

Equal to `extends` and `include` in Jinja2, but do not add quotation mark for a template file path.

```jade
extends base
extends base.jade
// equal to {% extends 'base.jade' %}

include include/comments
include include/comments.jade
include include.comments.jade
//
  equal to {% include 'include/comments.jade' %}
```

### macro/mixin

`macro` is a concept in Jinja2, `mixin` is a concept in Jade,  and in FarBox API they are equal.

`macro` means to define a cuntion, then it can be called in your template. If you need to cache the reuslt computed by the function, the name of function should start with `cache_`.

Jinja2 is a template frame wrote in Python, so the definition of `macro` is close to the way in Python.

```jade
mixin sidebar(name)
	h1= name
	p this is sidebar

// equal to
{% macro sidebar(name) %}
	<h1>{{ name }}</h1>
	<p> this is sidebar </p>
{% endmacro %}
```

If a macro definition and usages are in the same template file, just call it directly.
But if between different template files, you need to import it first. This is a concept from Jinja, so we need to use Jinja2 syntax now.

For example:
```jinjia2
{% from "mixins.jade" import get_url %}

{# Notice! `mixins.jade` needs quotation mark #}
```

**`macro` has a better performance to `include`， `include` brings a namespace definitionally, some unexpected errors may come with it. But `macro` is just a function, its namespace is private, and much more controllable.**



## Mix Jade and Jinja2

### Some Examples

In the `.jade` template file, we can use Jinja2 syntax in a separated line too.

```jade
extends base
{% from "mixins" import make_posts_list %}
{{ site.title }}

.entry
	| {{ post.title }} -
	| {{ post.date }}

```

### Keywords of Variables

'post', 'posts', 'site', 'sites', 'files', 'tags', 'tag', 'folder', 'folders','category', 'images', 'albums', 'album', 'next_one', 'pre_one', 'paginator', 'pager', thy are the keywords of variables, will not be treated as a HTML tag.

For example:

```jade
/*valid, the last result: <div>{{post.content}}</div>*/
div
	post.content


/*doc is not a keyword, the last result: <div><doc class="content"></doc></div>*/
div
	doc.content

/*if you need `doc` work well like `post`, the syntax below:*/
div= doc.content
/*or*/
div
	| {{ doc.content}}

```

### Keywords of Functions

'load', 'set_content_type', 'redirect', 'a_with_selected', 'set_per_page', 'add_doc_actions', these functions are core functions of FarBox, you can use them directly.

'need_login', 'need_admin', 'raise_404' are speical functions, for example, `need_login()` and `need_login`, euqal to `{{ need_login() }}`.



## Auto-Complete for Jade

- If doctype is not declared, it would be `doctype html` by default.
- If the doctype is `html`, and no feed address declared, it will be auto-added.
- If the doctype is `html`, and no charset declared, it will be `utf8` by default.



[jade]: http://jade-lang.com
[jinja]: http://jinja.pocoo.org/docs/
[jinja-cn]: http://docs.torriacg.org/docs/jinja2/templates.html


