Title: Functions and Processing
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

### put

Accepts three parameters, `path`, `content`, and `from_url`(means download content from which URL, default value is None).

`put('hello.txt', 'this is content')`, will create or modify the file whose path is `hello.txt`, set the content to `this is content`, and the file will be synced to Dropbox.

Notices：
1. In one page, no more than 2 times to call this function;
2. No more than 1,000 times to call in 5 minutes.
3. Be careful to use `from_url`, if the system think your behavior is evil, your account will be suspend.

### append

Append some content into a specified file, accpets these parameters:

- path: file path (required)
- content: the content to append (required)
- lines: default value is 1, maximum is 10, means the space lines to old content
- check: default is `False`, if the value is `True`, the duplicate operation ( to the last one) will be ignored
- reverse: default is `False`, means append at the tail of a file; if `True`, means head

## For Developers

> The functions for developers maybe open for some speical type accounts in future.


### get_hash

Accept any parameters, but `hashlib`(default value is md5) and `sort` are special; `hashlib` could be 'md5' or 'sha1', `sort` means sort the parameters or not.

code snippet (verify the request for WeChat):

```jade
rv = request.values
if rv.signature != get_hash('<your token>', rv.nonce, rv.timestamp, hashlib='sha1')
    +raise_404()
```

### get_outbound_link_password

Get the outbund link password for images, and need to join it into current URL, as the parameter (in GET method) of `outbound_link_password`.

This function accepts one parameter `days`, means the expiry date of the outbound link password. Default value is 3 days, and the range is 1-60(days).


### get_var/set_var

The functions can store or read some variables into the site. But the variables will not sync to Dropbox.

key and value can be too long (less than 64 bytes), the total count of variables should less than 100. if the value is a number, it should be between plus or minus 4294967296.

for example, set_var('test', 1), then get_var('test') will return `1`.

### send_request

Send a request to a outside URL.

Accept these parameters:

- url: outside URL
- method: default is GET, you can also use POST
- headers： default value is `{}`, set some special header information
- others k/v： for example, `send_request(<url>, k1=v2, k2=v2)`, accept any parameters in k/v format; if current method is GET, the parameters will be dumped into the URL, if is POST, will join into the data to send.

send_request will return a response object, `response=send_request('http://google.com/')`, in this way, the response will have these fields:
- text: returned data, in TEXT format, maybe just in binary.
- status_code: status code of this HTTP request
- cookies: Cookie written by the target URL in this request
- headers: headers of this response
- json: if the mimetype is json, call it directly
- ok: current response successed or not


Notices:

1. If our servers think your behavior is attacking target outsite URL, your account will be suspend.
2. You can't use this function to make a proxy.
3. No more than 3 times to call in one page; no more than 200 times in 5 minutes.
4. Do not request a big size file (like an image), the request will be timeout first, and your behavior will be marked as a danger.

### timestamp_diff

`timestamp_diff(<a unix timestamp>)`, returns the interval seconds between current and the specified time.

### cronjob

Accepts one parameter `hours`, means every ? hours to run this page, minimum is 1, maximum is 30.

If a page includes this function, the scheduled task will be activated when the page is visited at first time. Then the page will raise 400 error for the future visits, because it runs in backend now.


## Page Rendering Functions

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

### set_no_inject

This function means do not insert any codes or scripts, like a Mathjax script or a built-in script for AutoReload.


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




