Title: The Functions
Position: 3.9
url: functions
toc: yes


> The syntax to call a function: {{ function_name(v1, v2, v3=something, .etc) }}.
> **Note: Functions are running after default variables!!**

## Doc Processing

### has
Has some data/file or not, accept one parameter.

1. has('posts') site has posts or not
2. has('images') site has images or not 
3. has('categories') site has categories of posts or not
4. has(`<doc_path>`), site has a specified doc or not

### add_doc_actions

The `doc` here refers post/image/file.

This function returns nothing, but makes the doc object get some extral abilities, such as statistics and comment.

```
{% set post=get_doc(type='post', path='this-is-article.txt') %}
{{ add_doc_actions(post) }}
```
Specially to a post (status=`public`), add `action=xxx` to the URL to return some other kinds of data.

1. `URL?action=show_raw`， return the orignal content.
- `URL?action=show_json`, return the post object in JSON format.
- `URL?action=edit`, redirect to the Web Editor


## Page Rendering Functions

### SCSS/SASS/LESS

FarBox supports to compile SASS/SCSS files to CSS files automatically.

For example, the `template/styles.scss`: only when visiting `http://yoursite.com/template/style.scss?format=raw`, the orignal format will be kept, otherwise, it is in CSS format.

The supports for LESS is relatively limited, like the syntax of `extend` is not supported.

One more thing, when compiling a SASS/SCSS file, the logic like `include/import` will be ignored.


### load

This is a function for a lazy coder, it can help us to load multi-css-js files, splited by a blank space.

For example, `load("/hello.css /word.js")` will be compiled to the HTML code blow:

```html
<link  type="text/css" rel="stylesheet" href="/hello.css"/>
<script type="text/javascript" src="/world.js"></script>
```

There are some built-in shortcut keys for some special resources：

| name | Lib | Version |
| ----- | ---- | ---- |
| jquery | Jquery | 2.0.3 |
| font | Font Awesome | 4.0.3 |
| fonts | Font Awesome | 4.0.3 |
| mathjax | MathJax | 2.3 |
| pure | Pure | 0.3.0 |
| essage | Essage | - |

For example, `load("jquery")` will load a version 2.0.3 Jquery script.


#### How to call
> load is a lazy function, if the result is not correct, please try the orignal HTML code instead.

- load(`<path or shortcut>`)
- load(`<path or shortcut>`, version)
- load('`<shortcut1>#version <shortcut2>#version`')
- load(`<prefix_path>`, [sub_filename, sub_filename2])
- load( [`<path or shortcut>`, `<path or shortcut 2>`] )
- load('`<path or shortcut>` `<path or shortcut 2>`')
- load('`<path or shortcut>`, `<path or shortcut 2>`')
- load('`<path or shortcut>` `<path or shortcut 2>`, `<path or shortcut 3>`')




### get_request_path
Get a offset path, accept one parameter `offset`, and its default value is 1.

Assume visiting `/requested/path/sub_path` now, then：
1, get_request_path(offset=1) --> `path/sub_path`
2, get_request_path(offset=2) --> `sub_path`

There is a speical character `~`, it will always be the start point for computing offset.
Assume visiting `/requested/path/~sub_path/sub_sub_path` now, then:
1， get_request_path(offset=1) --> `sub_path/sub_sub_path`
2, get_request_path(offset=2) --> `sub_sub_path`

This function is usually together with [get_data](get-data), to get some URL related data.

### set_content_type

This function can change the type of response, it returns nothing. If your page is JSON/XML, it will be very useful.


For example: `{{ set_content_type('application/json') }}` -> a JSON page.

### redirect
Accept one parameter `url`,  `{{ redirect('http://google.com')}}` will jump to the homepage of Google.

### raise_404
Accept no parameter, will raise 404 error, and use template file `404.html` as a `Not Found` page.

## Permissions Related

### need_login
If current user is not login, force to jump to a page to login; otherwise, return nothing.

### need_admin
1. If current user is not login, force to jump to a page to login.
2. If current user is login, but not owner, raise an error.
3. If current user is the owner, return nothing.

## HTML Generation Function

###  a_with_selected

Generate a snippet of A tag HTML code, if the URL matched, the A tag will have a class named `selected`.
Accept four parameters: url, title, dom_id=None, equal=False.
- url, the value of href of the A tag
- title, the innerText of the A tag
- dom_id, if declared, the id value of the A tag
- equal, if not True and current URL starts (not only equal) with the `url` parameter, the class of A tag will be `selected` too.


### bread_nav

A function to turn a specified `path` into a breadcrumb navigation. Accept parameters: `path, prefix='', prefix_name='/', split_by='/'`.

- path，the path specified
- prefix, the prefix for all hyperlinks in the navigation
- prefix_name, the title of the first node
- split_by, the connector

Assume `<path>` is `/folder 22/img_3851.jpg`； 
then `{{ bread_nav(path,'/folder/', prefix_name='Albums>'), split_by='>' }}` turned into the HTML code below:
```html
<ul class="bread_nav">
 	<li>
 		<a href="/folder/">Albums></a>
 	</li>
	<li>
		<a href="/folder/folder 22/">folder 22  >  </a>
	</li>
	<li>img_3851.jpg</li>
</ul>
```




