Title: Template Engine and URL
url: template-engine
toc: yes

> FarBox's basic template engine is base on the [Jinja2][jinja], which is a template frame base on HTML.

## Basic Syntax

### Jinja2 Syntax

The Jinja2 syntax is similar to HTML, but you can use `{{}}` `{%%}` to embed some API code.

```
{{ a variable or run a function }}
{% logical and other operations %}
```

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

### Template Path

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


## URL Match Rules

All the URLs of a site are decided by the paths of template files.

For example, the template file has a path `template/post.html`, and it will handle the URL `http://domain.com/post`.


### Orders of URL Match

> The filename of template files in examples below ends with `.html`, in fact, `.jade` is equal to `.html`.

if a URL ends with `.txt` `.mk`  `.md` `.makrdown`, the Template Engine will try to find a post with the same path, and will ask `template/markdown.html` to render it; if the template file not exists, then continue.

1. step1, try to match index.html for homepage.
- step2, try to match the full URL, for example `/path/sub_path.html` will match `/path/sub_path`.
- step3, try to match the first path of URL, `first_path.html` will match `/first_path/sub_path`, if there is no template file `/first_path/sub_path.html`.
- step4, try to use `I-Love-FarBox.html` to match all the URLs.(*be careful to use this rule*)
- step5, finnaly, will use `404.html` as `Not Found` page.

**Tip: `first path of URL` means the characters before first `~` or slash**
```
/path/sub_path the first path is `path`
/path/sub_path/~hello/world the first path is `path/sub_path`
```

### Mixed URL

If `+` in the name of a template file, it can match multi-URLs.

For example `template/index+tags+archive.html` can match `HomePage(/)`、`/tags`、`/archive`.


[jade]: http://jade-lang.com
[jinja]: http://jinja.pocoo.org/docs/
