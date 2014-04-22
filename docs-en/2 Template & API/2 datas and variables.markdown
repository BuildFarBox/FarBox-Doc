Title: Get Data and Variables
url: get-data
toc: yes


# Get Data

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


## get_comments

Accept parameters `<parent>` `<limit>` `<sort>`.

If `parent` is not set, then get all the comments of current site.

Code snippet to get comments for a post:
```
    {% set comments = get_comments(post) %}
```

## is_comment_allowed

Accept a `doc` as parameter, means new comments allowed to this doc or not. Usually using
context_doc as parameter, such as `{{ is_comment_allowed(context_doc) }}`.

context_doc was set automatically after function `add_doc_actions` called.


# Default Variables

> Besides global variables like `site`、`request`, FarBox offer some other defalt variables (most of them come from `get_data`) for API.

## Global Variables
### site
Current site, the detail properties, refer [Site](data-types#toc_1)

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

### request

The information about current request, details refers to [Request](data-types#toc_5)

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


## Shortcut Variables

> `shortcut variables` are generated automatically from the `get_data` function. If your page logic is complicated, please directly use `get_data` instead.
> Be careful to use shortcut variables in `if/else`, for example `{% if posts %}` means nothing, because `posts` is always with some records.

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

