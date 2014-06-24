Title: Get Data
url: get-data
toc: yes


# Get Documents

## get_data
> This is main function to get data in FarBox, it allows to get your directories and files like querying a database.
> And not all the parameters are required when calling this function.

### Parameters Accepted

#### types
There are five different types document, post (default value)/folder/image/file/template. You can use `+` to join multi-types, like `post+folder` which is equal to `['post', 'folder']`.

#### type
Similar to types, but not allowed `+` in it, just one type one time. If `type` is declared, the `types` won't be valid.

#### path
Default value is `/` (means under the root of site folder), if `path='docs/'`, all the documents returned must be under `<site-folder>/docs/`.

#### level
Works together with `path`, means the depth of a path. If level=1, means only the direct subfolders; if level=2, will include the subfolders of subfolders; `[2, 3]` is acceptable too, it includes level=2 and level=3, but exclude other deeper path, and exclude level=1 too.

And `>n` or `<n` is acceptabe, `>1` means level=2, level=3, level=4 .etc .

#### sort
The sort type below:
- `desc`, DESC order by `date` (default value)
- `asc`, ASC order by `date`
- `position`, ASC order by the `position` field that owner customed
- `score`, the recent hotest documents sorted by the dig score
- `_score`, same to `score`, but the dig score is customed by the owner
- `-m_date`, DESC order by `m_date`, a new comment on a document will update the m_date

#### limit
How many records to fetch, if limit=1 and with_page=False, then the result returned will be a document object, not a list.

#### page
Records needed on which page number, default value is `1`, or get the value from URL `/URL/PATH/page/<page_number>` automaticly.

#### with_page
Use paginator or not, default value is `True`, then the `limit` will be equal to  `objects count per page`.

#### pager_name
**Mark a paginator, recommended!** When rendering a page, there may be many paginators (produced by calling `get_data` and with_page=True), if using `pager_name`, you can get the paginator by the this name lately.

#### date_start
The date of documents must be newer than the declared date (exclude `equal`).

#### date_end
The date of documents must be older than the declared date (exclude `equal`).

#### min_posts_count
A document must include more than `min_posts_count` posts, works for `post` and `folder`, the posts_count of all `post` is 1.

#### min_images_count
A document must include more than `min_images_count` image, works for `image` and `folder`, the images_count of all `image` is 1.

#### return_count
Default value is `False`, if set to `True`, the result returned will be an interger that means the documents count matched in total.

### Parameters Only for `post`
#### status
The post's default status is `public`, other values are customed by the author.
`all` is a special value, the result will include all posts. So, do not set a status named `all` for post.

If the parameter of `keywords` is not declared, then the `status` can be a list, like `status=['public', 'protected', 'my_status']`.

#### url_path
If using `url_path`, means with_page=False and limit=1, only return one post if exists.

#### tags

Find posts matched the tags that customed by the author in the metadata. `'tag1+tag2'` is equal to `['tag1', 'tag2']`.

#### keywords: Fulltext Search

This parameter means a fulltext search, the length of `keywords` should be less than 50. And it will search `title` and `content` of a post.

When using `keywords`,  min_images_count/min_posts_count are not valid.

And the parameter of `sort`, _score、score、-m_date are not supported. There are two new values for `sort`, `slot2` and `-slot2`(DESC).

**Note: slot1 & slot2 are two properties for extending keywords search, slot1 is a search field and slot2 is a sort field.**


#### search_fields

Working together with `keywords`, default value is `['title', 'raw_content']`, means to search keywords in title and content of a post.

A extral field is `slot1`, which is customed by author in the metadata of a post.

### Notices

- Declare the parameters on demand.
- The parameter of `limit`, means to generate a paginator object; if with_page=False or a declared `page`, then paginator won't be generated.
- If a parameter is only valid for a special type, then a mixed types will not work. For example `get_data(types='post+folder', status='public')` will return posts only.



## get_doc

Get a document by `path`, the `path` is the only parameter acceptable.

## get_connected_one
Get a document which is before or after (computed by the date) of the specified document that has same document type. Acceptable parameters are `<doc>` `<same_folder>` `<position>`.

`doc`: a document to specify.
`same_folder`: None/True/False. If True, means the document you need should be in the same folder; If False, try to find it under the whole site folder. If not declared or set to  `None`, `whole site folder` for posts, `same folder` for other documents.
`position`: `>` or `<`, means next one or previous one.

In fact, 'get_connected_one' is using `get_data` to get result, the logical code on FarBox server looks like below:
```python
if 'date' not in doc:
    return
if (same_folder is None and doc.get('type') == 'post') or not same_folder:
    path = None
else:
    path = '/'.join(doc.get('path').split('/')[:-1]) + '/'
if position == '>':
    return get_data(type=doc.get('type'), path=path, limit=1, with_page=False, date_end=doc.get('date'), sort='desc')
else:
    return get_data(type=doc.get('type'), path=path, limit=1, with_page=False, date_start=doc.get('date'), sort='asc')
```

## Doc Properties

> **Doc includes all the resources under your site folder, like posts, images, CSS/JS files, and folders.**

### Basic Properties

| Name | Description |
| ----- | --- |
| path | relative path to site folder, lower-case |
| full_path | full path of document, lower-case |
| raw_path | same to full_path, but preserving the case |
| parent_path | parent path of `path` |
| parent_full_path | parent path of `full_path` |
| parent_raw_path | parent path of `raw_path` |
| full_path | the full path of the file|
| type | document type, like folder/post/image/file/template |
| title | document title |
| date | the date of document, if a post, means the publish date |
| m_date | last modified date, a new comment on the document will make m_date updated |
| raw_date| last modified date in system |
| position | a customed position by owner for sorting, a float number |
| size |  file size |
| content_type | document's mimetype, like `image/jpeg` |
| visits | visits (page views) of the document |
| _visits | approach to visitors |
| content |  most time is the raw content of the document |
| ext | filename extension like `jpg` `txt` |

### Properties Only For `post` Type

> `post` type, refers to the documents whose suffix is .txt/.mk/.md/.markdown.

| Name | Description |
| ----- | --- |
| content | body text, HTML format |
| toc | TableOfContens, HTML format |
| status | status of post, default value is `public` |
| cover |cover for post, the first image in the main body |
| raw_content | raw content of file, plaintext format, excludes metadata strings|
| _content | raw content of file, includes metadata strings |
| metadata | metadata of post, the extral properties |
| tags | tags of post, a List|
| url_path | url path |
| url | '/post/’+url_path |
| comments_count | comments count |
| posts_count | always equal to 1 |

Notes:
- If the `url_path` is not customed, the system will generate one instead base on the filepath.
- If there is no image in post, the `cover` will be None.
- `url` is a default value to refer the URL of a post, but if the `post detail template file` is not `template/post.html`, the `url` won't work.


### Properties Only for `image` Type

| Name | Description |
| ----- | --- |
| url  | address of the image |
| image_width | -- |
| image_height | -- |
| exif | the exif information, a dict type object |
| comments_count | -- |
| images_count| always equal to 1 |


#### image.exif Properties

| Name | Description |
| ----- | --- |
| make | like `Apple` |
| model | like `iPhone 4` |
| datetime | original dateTime, like `2012:10:17 18:22:14` |
| fn | aperture value |
| flash | --  |
| focal_length | -- |
| exposure | exposure time |
| iso | ISO value |
| program | 0-8 |
| latitud |  south latitude is a negative value |
| longitude | west longitude is a negative value |
| altitude | -- |
| height | original image height |
| width | original image width |


### Properties Only For `folder` Type

| Name | Description |
| ----- | --- |
| content | body text, HTML format |
| raw_content | raw content of file, plaintext format, excludes metadata strings  |
| _content | raw content of file, plaintext format, includes metadata strings |
| metadata | the extral properties |
| posts_count| how many posts in it (includes in sub folders) |
| images_count| how many images in it (includes in sub folders) |
| _posts_count| how many posts in it (excludes in sub folders) |
| _images_count| how many images in it (excludes in sub folders) |

> The properties `content/raw_content/_content/metadata` are from a document named `index.txt` in the folder.

# Other Related Data

## get_paginator
This function help us to get a paginator, accept one parameter `index` (integer type), default value is 0, means the first paginator of current page.
 
If `index` is a string type, FarBox will treat it as a `pager_name`, which one is set in the function `get_data`.

When calling `get_data` or `get_comments`, if there is no `page` parameter, a new paginator will be generated. If using `get_data`, and `with_page=False`, no paginator generated either.

Code snippet:
```
{% set posts = get_data(type='post', limit=3, pager_name='posts') %}
{% set images = get_data(type='image', limit=10) %}
{% set p1 = get_paginator(0) %}
{% set p1_2 = get_paginator('posts') %}
{% set p2 = get_paginator(1) %}
```
`p1` is the paginator of posts, 3 records per page; `p2` is the paginator of images, 10 records per page. And `p1_2` is euqal to `p1`.

The paginator we got (p1 or p2), will compute page_number from URL automatically. For example, when visiting `/URL`, the page=1; and `/URL/page/2`, the page=2.

**Note: Avoid using multi-paginators in one page, because the page_number from URL is shared by every paginator.**

### Properties List
| Name | Description |
| ----- | --- |
| per_page | the objects count per page |
| object_list | objects of current page, like `posts` `files` |
| total_count | objects count in total |
| total_pages | -- |
| page | current page number |
| previous_page | number of previous page |
| pre_page | same to previous_page |
| previous_page_url | -- |
| pre_page_url | same to previous_page_url |
| next_page | number of next page |
| next_page_url| -- |
| has_next| True/False |
| has_previous | True/False |  
| has_pre | same to has_previous |  

### Code Snippet 

A common code sinppet to handle the paginator:

```
{% if paginator.has_previous or  paginator.has_next %}
    <div class="pager">
        {% if paginator.has_previous %}
            <a class="round pre" href="{{ paginator.previous_page_url }}">Pre</a>
        {% endif %}
        {% if paginator.has_next %}
            <a class="round next" href="{{ paginator.next_page_url}}">Next</a>
        {% endif %}
    </div>
{% endif %}
```


## get_comments

Accept parameters `<parent>` `<limit>` `<sort>`.

If `parent` is not set, then get all the comments of current site.

Code snippet to get comments for a post:
```
    {% set comments = get_comments(post) %}
```

### Comment Properties List

| Name | Description |
| ----- | --- |
| content | comment content, HTML format |
| raw_content | comment content, plaintext format |
| author | author name  |
| site | the site url of commenter |
| date | comment datetime  |
| email_md5 | the md5 value of commenter's email address |
| ip | IP of commenter |

> comment.email_md5 can help to get the avatar of a author，like http://www.gravatar.com/avatar/{{ comment.email_md5 }}




# Default Variables

## site

> current site on visiting.

### Properties List

| Name | Description |
| ----- | --- |
| _id | site_id |
| title | site title |
| site_path | in lower-case, `/site_folder_name/` |
| domains | site domains, a List |
| tags | tags of (public) posts, a List，tag_name+tag_count |
| avatar | avatar of site owner |
| configs | site configs |
| content | site description (HTML) |
| raw_content | site description (TXT) |
| template_key | Template Package Key inherits from |
| owner_template_key| Template Package Key the site owns |
| config_path | current site config file path, may be site.txt |
| comment_script | content of comment_js.txt(md/mk/markdown) (TXT) |

### site.tags

1，site.tags is a list of `(tag_name, tag_count)`, sorted by `tag_count` looks like, :
```
[('tag 1',3), ('tag 2', 1)]
```
2，code snippet for site.tags:
```
{% for tag_name, tag_count in site.tags %}
{{ tag_name }}, {{ tag_count }} </br>
{% endfor %}
```

### site.title

The default value is your site folder name or equal to `site.configs.title`.



## request

> the request (broswer) information of current user.

### Properties List
| name | type | description |
| ----- | --- | --- |
| form | dict | the incoming values through POST method|
| args | dict | the incoming values through GET method |
| values | dict | merge form & args |
| method | string | like `GET` `POST` `PUT` .etc |
| json | dict | if the mimetype of client sent is `application/json` |
| is_login | boolean | login or not |
| is_admin | boolean | login user is the site owner or not |
| language | string | lower-case, the language of client's browser like `zh_cn` |
| path | string| - |
| url | string | - |
| base_url | string | - |
| url_root | string | - |
| url_without_host | string | - |


**Note，is_login/is_admin is also stored into Cookie of client's browser.**

### request.path

When visiting `http://www.example.com/page.html?x=y`，the `request` has some variables below:

| Name | Value |
| --- | --- |
| path | /page.html |
| base_url	| http://www.example.com/page.html |
| url	| http://www.example.com/page.html?x=y |
| url_root | http://www.example.com/ |
| request.url_without_host |/page.html?x=y |


## Common Variables

> `common variables` are generated automatically from the `get_data` function. If your page logic is complicated, please directly use `get_data` instead.
> Be careful to use them in `if/else`, for example `{% if posts %}` means nothing, because `posts` is always with some records.

### next_one
equal to `get_data(type=context_doc.type, date_end=context_doc.date, sort='desc', limit=1, page=1)`

### pre_one
equal to  `get_data(type=context_doc.type, date_start=context_doc.date, sort='asc',limit=1, page=1)`

### categories
The categories of posts, equal to `get_data(type='folder', level=[1,2], min_posts_count=1)`.

### cateogry
If the URL is `/any_thing/<folder-path>`, FarBox will try to find a folder.**Note, the <folder-path> must end with `/`**

And it's equal to `get_doc(path=path2)`.

### folder
Same to `category`.

### tags
Equal to `get_request_path(offset=1).split('+')`
When visiting `/any_thing/tag1+tag2`, then {{ tags }} will return `['tag1', 'tag2']`.


### posts
Case 1: when `category` matched a folder, means the posts under the category; equal to `get_data(type='post', path=category.path)`;

Case 2：when `tags` matched, means the posts marked the `tags`; equal to`get_data(type='post', tags=tags)`;

Case 3: If current URL is `/feed`, output the recent ten posts (the status is `public`); equal to `get_data(type='post', page=1, limit=10)`；

Case 4: If current URL looks like `anything/?s=keywords`, equal to `type='post', keywords=request.args.get('s'))`;

Finnaly: If nothing matched for `posts`, output the posts (status is `public`) under current site.

### images
Case 1: when `folder` matched a folder, means the images and albums under the folder; equal to `get_data(types='folder+image', path=folder.path, min_images_count=1, level=1)`;

Case 2：all the images and albums under currente site，equal to `get_data(types='folder+image', min_images_count=1, level=1)`。


### albums
Case 1: when `folder` matched a folder, means the albums under the folder; equal to `get_data(types='folder', path=folder.path, level=1, min_images_count=1`；

Case 2：first 100 albums under the site, equal to `get_data(types='folder', min_images_count=1, level=[1,2], limit=100)`

### post

Get a post whose `url_path`=`path2`.

Equal to:
`{% set post = get_data(type='post', url_path=get_request_path(offset=1), limit=1, page=1) %}`, then `{{ add_doc_actions(post) }}`

### file
Get a file whose `path`=`path2`.

Equal to:
`{% set post = get_doc(path=get_request_path(offset=1)%}`, then `{{ add_doc_actions(post) }}`。


### paginator

equal to `get_paginator(0)`, if you find the paginator is different in you mind, try to use the function `get_paginator` instead.

Code Snippet:
```jinja2
{% for post in posts %}
    {{post.title}}
{% endfor %}
{{ paginator.page }}
```


### pager
same to `paginator`

### comments
equal to `get_comments()`



## Other Variables

### sites
The sites list that current login account owns.

### account_id
The id of current login account .

### account
The information about  current login account.

| Name | Description |
|  ---  | --- |
| service | Cloud Service, current is Dropbox only |
| uid | UID |
| utc_offset | timezone, integer |
| created_at | a datetime |
| last_login_at | a datetime |
| synced_at| last synced datetime |
| name | name computed from email address |
| email_md5 | MD5 value of email address, usually used to get avatar |



### context_doc

A context document, after running add_doc_actions(`doc`), the `doc` will be the context_doc.

### raw_comment

A comment object commited by a user through browser.

| Field | Description |
| --- | --- |
| content | comment content in plaintext format |
| author | commenter's name | 
| email | commenter's email |
| site | commenter's site |
| error | if comment failed, the reason |


### path1
The first part of a URL path.
```
'/abc/de/fad' --> abc
'/abc/de/~fad' --> abc/de
```

### path2
Exclude the `path1`, the rest parts of a URL path.
```
abc/de/fad' --> de/fad
'abc/de/~fad' --> fad
```

### interface

The interface configs for site admin dashboard,.

### template_key

The template_key of the template package which current site inherents from.

### template_packages

A list of template packages that maintained by FarBox team.

### template_package

Return a template package object if current site has one.



# Special Format

When rendering some fields/properties in a page, multi-formats will be needed. To make calling API much more humanistic, we wrapped some special data types.

## Date

Use `{{post.date}}` directly, return a strings like `2012-12-22 10:11` by default.


### format

Combine strings in `TimeFormate` to get a formated date strings.

code snippet:
```
    {{ post.date.format('%Y-%m-%d') }}
   # will be 2012-12-22
```

### TimeFormat
	
```
%a	Locale’s abbreviated weekday name.	 
%A	Locale’s full weekday name.	 
%b	Locale’s abbreviated month name.	 
%B	Locale’s full month name.	 
%c	Locale’s appropriate date and time representation.	 
%d	Day of the month as a decimal number [01,31].	 
%H	Hour (24-hour clock) as a decimal number [00,23].	 
%I	Hour (12-hour clock) as a decimal number [01,12].	 
%j	Day of the year as a decimal number [001,366].	 
%m	Month as a decimal number [01,12].	 
%M	Minute as a decimal number [00,59].	 
%p	Locale’s equivalent of either AM or PM.
%S	Second as a decimal number [00,61].	
%U	Week number of the year (Sunday as the first day of the week) 
%w	Weekday as a decimal number [0(Sunday),6].	 
%W	Week number of the year (Monday as the first day of the week)
%x	Locale’s appropriate date representation.	 
%X	Locale’s appropriate time representation.	 
%y	Year without century as a decimal number [00,99].	 
%Y	Year with century as a decimal number.	 
%Z	Time zone name (no characters if no time zone exists).	 
%%	A literal '%' character.
```

## Text

> only valid for the property of `content`

### plain

Output the plain text includes linebreaks only, if there is some HTML tags in the post, they won't be passed.

### safe_plain

same to `plain`, but the HTML tags will be escaped to the safe strings.


### without_toc

for `post` type only, contents without toc (table of contents).

### opening

If `<!--more-->` in `content`, the content will be split by it and return the first part only.

If you using `{{ post.content.opening }}`, but no `<!--more-->` in content, will return nothing.

### limit

Function `limit` can get a section of content, it accept tow parameters, `length` and `with_mark` (default value is `.......`).  If content is longer then the `length`, will return `the_section + with_mark`.

The text object returned has two extra properties, `more` (boolean type) and `no_pic`.

```jinja2
{% set content = post.content.limit(100, '...')  %}
	<div> {{ content }} </div>
{% if content.more %}
	<a href="#">Read More</a>
{% endif %}
{{ content }} {# just the content return, maybe include images #}
{{ content.no_pic }} {#  the content without images #}
```

### Oters

```
post.content[0:100] # from 0-100, plaintext format
post.content(100) # from 0-100 too, but in HTML format

```

## ImageURL

> Only available for the `url` property of a `image` type document.

`image.url` can resize the image size, syntax is `image.url.s<width>x<height>` or `image.url.s<width>x<height>xfixed`.


Examples:

```
{{ image.url.s50x50xfixed }} will return a 50x50 fixed-size thumbnail, and it is equal to {{ image.url['50x50xfixed']}};

50x50, a thumbnail whose max_width and max_height is smaller than 50 pixels;

0x50，max_height smaller than 50 pixels;

0x50xfixed，the thumbnail height alwasy be 50 pixels.

.etc

btw, {{ image.url.s50x50xfixed }} is equal to `{{image.url}}?width=50&height=50&fixed=true`.

```

And `{{ image.url.blur }}` will return the url of a blured image.

If width=550, and FarBox will turn it into a valiable value `640`.

| Parameter | description | valiable values |
| --- |--- | --- |
| width | image width | [40, 80, 160, 214, 320, 428, 640, 960, 1280] |
| height| image height | [40, 80, 160, 214, 320, 428, 640, 960, 1280] |


The max size of an image is `1280*1280`, if the orignal size of image is larger than this, you need to use `image.url.large` or `{{ image.url }}?large=true`,  and the max size of an orignal image is `1560*2560`.


## List

The functions below, avaible for a object whose type is `list`. A list function usually calls traverses multi-times, so be careful to use them, otherwiste Timeout error will happen to your site.

### sort_by

Accept one parameter `attr`, resort the list by the attribue. if the `attr` starts with `-`, the result will be resorted in DESC order.

```
{{ posts.sort_by('position') }}  # resort by customed position
{{ posts.sort_by('-position') }} # resort by customed position in DESC order
{{ posts.sort_by('date.year') }} # resort by the year of the publish date of the post
```

### group

Group the list, accept one parameter `attr`, if the `attr` starts with `-`, the result will be resorted in DESC order.

If you want to use multi-sub-properties, the `attr` should follow this format `attr:sub_att1+sub_attr2`, such as `posts.group('date:year+month')`.

If the type of a sub-property is a list too, each of the sub-property values will be grouped, such as `posts.group('tags')`.


The returned value is a list too, each item in the list looks like：`[group_key, [item1, item2, item3, .etc]`.

- - - - - - -

Code snippet:

```html
{% set posts = get_data(type='post', limit=300, sort='desc') %} 
{% if request.args.use == 'tags' %}
    {% set entries = posts.group('tags') %}
{% else %}
    {% set entries = posts.group( 'date:year') %}
{% endif %}
```

The `entries` that returned looks like：
```json
[
    (2013, [<post1>, <post2>, <post3>]),
    (2012, [<post1>, <post2>]),
]
```  

Then we embed the data into the page.
```html
<ul>
    {% for year, posts in entries %}
        <li>{{ year }}</li>
        <ul>
        {% for post in posts %}
            <li>{{ post.title }}</li>
        {% endfor %}
        </ul>
    {% endfor %}
</ul>
```


**Note：The `sort` refers the order of groups, not order of the items in groups. Make sure the original data is a sorted list before calling `group` function.**


### filter

Filters a list, then returns a new list. Accept two parameters, one is `attr`, the other is `attr_value`, if the value of the `attr` is equal to the `attr_value`, the item will be appended to the new list.

Assume objects = [{value:1}, {value:2}, {value:1}]

Then `objects.filter('value', 2)` will return `[{value:2}]`.

### pick

Pick some special values of a speical attribute.

```json
objects = [
    {path: "1"},
    {path: "2"},
    {title: "hello"}
]
```

Then `objects.pick('path')` will return `['1', '2']`.

### add

`add(obj, position=-1)`, add an item into the list, default `postion=-1` means insert at the foot.

And this function will try to avoid items conflict. For example:

```
posts = [<p1>, <p2>, <p3>]
new_post = <p2>
posts.add(new_post, 0)

// at the last, posts = [<p2>, <p1>, <p3>]
```


## json

If the type of a object is `list` or `dict`, the `json` property will be available. It turns object into json format. If turning fails, the orignal object returned.

Code snippet:

```
{% set posts = get_data(type='post', limit=10) %}
{{ posts.json }}
```






