Title:  Data Types & Properties
url: data-types
toc: yes


# Data Types

## site

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



## Doc

> **Doc includes all the resources under your site folder, like posts, images, CSS/JS files, and folders.**

### Basic Properties

| Name | Description |
| ----- | --- |
| path | relative path to site folder, lower-case |
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


##  comment

### Properties List

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



## paginator

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
            <a class="round pre" href="{{ paginator.previous_page_url }}">上一页</a>
        {% endif %}
        {% if paginator.has_next %}
            <a class="round next" href="{{ paginator.next_page_url}}">下一页</a>
        {% endif %}
    </div>
{% endif %}
```

# Format Variable and Property

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



