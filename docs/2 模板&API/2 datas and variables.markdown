Title: 数据获取与各变量
url: get-data
toc: yes


# 数据的获取

## get_data

> 这是FarBox中获取数据的主要函数, 它让你调用数据库的逻辑去查询自己的文件夹与文件。
> 所有的参数，按需调用即可。

### 接受的参数

#### types
FarBox中有五种文档类型, post（为默认值）、folder、image、file、template, 可使用`+`连接，比如`post+folder`，或者['post', 'folder']

#### type
同types，但不接受`+`号连接, 仅能声明一种类型，并且使用了这个参数后，types参数将会无效。

#### path
默认为`/`(相当于站点根目录), 限定查询数据的路径，比如`path='docs/'`, 则表示仅查询docs/这个目录下的数据。

#### level
配合path使用的，表示所查询path的深度。如果level=1，表示仅查询到其子目录，level=2，则仅查询子目录的子目录；也接受[2,3]这样形式的值，表示查询所有2级以及3级的目录，但不包含更深的路径，也不包含1级子目录。

另外接受`>n` 或者`<n` 类型的参数，比如`>1`，表示2级目录或以上。

#### sort
排序类型，有五种值可选：
- `desc`, 按时间倒序（默认值）
- `asc`, 按时间正序
- `position`, 按照文档的自定义位置排序（正序）。
- `score`, 按照默认的dig灵敏度排序的`最近热文`
- `_score`,  按照自定义灵敏度排序的`最近热文`
- `-m_date`, 按照文件最后修改时间倒序，评论文章，也会导致m_date的变动。

#### limit
一次取出多少条数据，如果limit=1并且with_page=False，那么返回的结果不再是列表，而是唯一一条的数据对象本身。

#### page
取得第几页的数据，如果没有声明，则默认为1或者取URL中形如`/URL/PATH/page/<page_number>`作为page的参数。

#### with_page
是否启用分页；如果为True（默认值），则limit将会被当做每页多少条。

#### pager_name
**分页标记值, 建议使用这个参数！** 页面在渲染过程中，可能会产生多个分页对象，调用get_data的时候可以为新创建的分页对象指定名称；这样在后续调用分页数据的时候，就不容易产生混乱。

#### date_start
文档的时间(即date字段)大于某个时间（不包括等于的情况）。

#### date_end
文档的时间小于某个时间（不包括等于的情况）。

#### min_posts_count
最少包含多少篇日志，针对post、folder类型的文档有效，所有的post类型，posts_count都为1。

#### min_images_count
最少包含多少张图片，针对image、folder类型的，所有的image类型，images_count都为1。

#### return_count
默认为`False`, 如果设为`True`，那么整个查询不返回结果，仅返回一个匹配到的文档数。


### 仅针对post类型有效的参数
#### status
默认的post状态为`public`, 其它值都是由文章作者按需自定义的。
status中有一个值是特殊的，为`all`; 它代表输出所有的状态。所以，需要注意下，不要用`all`来定义某一篇post的status。

如果没有传递keywords这个参数，那么status可以是一个列表, 比如`status=['public', 'protected', 'my_status']`。


#### url_path
自定义的url查询，如果使用这个参数，则with_page=False, limit=1, 也就是仅返回一条结果或者空值。

#### tags
用户给post打上的标签，以是`'tag1+tag2'`或者`['tag1', 'tag2']`，两者等价。

#### keywords: 全文搜索

这个查询是针对全文的搜索，关键词最多不超过50个字（会自动进行分词）。查询的范围包括标题、以及正文内容。

使用keywords的时候，min_images_count、min_posts_count这些参数不会生效。

sort参数，不支持_score、score、-m_date；如果有score排序的需要，可以在获取搜索结果后，重新排序即可。

当使用keywords参数后，另外可以使用两个新的sort参数，为`slot2`与`-slot2`(倒序)。

**说明，slot1 & slot2两个属性来专门用于搜索的扩展属性。其中slot1是搜索字段的扩展，slot2为排序字段的扩展。**


#### search_fields

这个是对`keywords`字段的补全，非全文搜索的情况下无效。默认值为['title', 'raw_content'], 表示对标题以及正文进行搜索。

除了`title`、`raw_content`外，另外支持的一个字段是`slot1`。

### 注意事项

- 各个参数，按需使用。
- 使用limit参数，会自动产生一个paginator（分页）的数据对象，如果使用limit的同时，也使用了page这个参数，则paginator对象不会产生。
- 当一个参数具有针对性的type的时候，那么多个types混合，其它类型的数据则无法获取，比如`get_data(types='post+folder', status='public')`，那么结果集中仅会有`post`的数据。

## get_paginator
这是一个获取分页的函数，接受单个参数`index`。index为第几个分页信息，默认为0，即第一个分页信息。

`index`也接受`字符`类型的参数，为`pager_name`；也就是在`get_data`函数中声明的分页标记值。

使用`get_data`或者`get_comments`的时候，并且传递的参数中没有`page`这个参数，那么，都会产生一个Paginator类型的分页。每页的数据条数，对应到`limit`这个参数。

如果使用的是`get_data`函数，并且传递了参数`with_page=False`, 则不会产生分页数据。


代码示例:
```
{% set posts = get_data(type='post', limit=3, pager_name='posts') %}
{% set images = get_data(type='image', limit=10) %}
{% set p1 = get_paginator(0) %}
{% set p1_2 = get_paginator('posts') %}
{% set p2 = get_paginator(1) %}
```

如代码所示，`p1`是posts的分页信息，每页3条；`p2`是images的分页信息，每页10条。另外`p1_2`与`p1`是同一个分页信息。

获得的paginator（p1或p2)，默认都会用页码来补全URL，比如，访问`/URL`的时候，那么`/URL/page/2`就表示第二页。

**注意：不要在同一个页面中混用多个paginator，因为URL传递的page页码对多个paginators来说，都是公用的； 如有特殊需求，则需要在get_data的时候，需要指定page这个参数。**


## get_doc

根据指定的路径获得一个文档/文件的函数。接受1个参数path。path不能为空。

## get_connected_one
获取doc相同类型的上一个、下一个（按照日期排序）文档数据，接受参数`<doc>` `<same_folder>` `<position>`。

`doc`: 文档对象，比如通过`get_doc`获得的值
`same_folder`: None/True/False, 如果是True，则表示与doc是位于同一个目录的；如果是False，则是整个网站的范围。如果不指定，即`None`， 表示如果是日志类型的，整个网站范围；反之，则是同目录范围。
`position`: `>` 或 `<`, 表示下一个或上一个。

'get_connected_one'实际上可以直接用`get_data`来获取，它在服务器上的逻辑代码如下:
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

接收参数`<parent>` `<limit>` `<sort>`.
如果parent为空，则会获得当前站点下的所有评论。
通常在文章详细页面中，使用`post`来做parent，从而获得当前文章的评论列表。
代码示例:
```
    {% set comments = get_comments(post) %}
```

## is_comment_allowed
接受一个doc参数，即当前的doc是否允许评论。一般会传入context_doc，比如`{{ is_comment_allowed(context_doc) }}`。

context_doc，是由add_doc_actions运行产生的。


# 默认变量

> 除了`site`、`request`等全局变量外，我们默认提供了一些变量供调用，这些变量多数是`get_data`的变体。

## 全局变量
### site
当前网站，具体属性，参照[Site](data-types#toc_1)

### sites
当前登录账户所拥有的站点列表。

### account_id
当前登录账户的ID

### account
当前登录账户的信息。

| 字段名 | 说明 |
|  ---  | --- |
| service | 云服务商，目前为Dropbox |
| uid | UID |
| utc_offset | 时区 |
| created_at | 创建时间 |
| last_login_at | 最后登录时间 |
| synced_at| 最后同步时间 |
| name | 根据其邮箱地址获取的名称 |
| email_md5 | 邮箱的MD5值，常用来获取头像 |

### request
当前页面的请求信息，具体属性，参照[Request](data-types#toc_5)

### context_doc
上下文数据对象，使用add_doc_actions这个函数后，会自动将add_doc_actions(doc)中的doc作为context_doc

### raw_comment
用户提交的评论。

| 字段 | 说明 |
| --- | --- |
| content | 评论内容 |
| author | 评论者称呼 | 
| email | 评论者邮箱 |
| site | 网站地址 |
| error | 如果评论出错，则是出错原因 |


### path1
针对当前访问路径，获取其第一部分。
```
'/abc/de/fad' --> abc
'/abc/de/~fad' --> abc/de
```

### path2
针对当前访问路径，获取其第一部分之外的。
```
abc/de/fad' --> de/fad
'abc/de/~fad' --> fad
```

### interface

模板包配置项中的interface，用以控制默认的网站的后台管理。

### template_key

网站正在使用的克隆模板的key值。

### template_packages

由FarBox官方提供的默认模板数据列表。

### template_package

返回当前网站在使用的模板包，如果有的话。




## 快捷变量
> 快捷变量是针对一些特殊情况，自动套用get_data函数获得的数据，如果页面的逻辑不是很简单的前提下，我们建议直接使用get_data来获取数据。
> 一般情况下，不要直接使用快捷变量作为if/else的判断逻辑，比如 {% if posts %}这个判断，如果posts不是自己通过get_data获取的，那么基本上都是有返回值的。

### next_one
按照时间的排序，下一个同类数据，比如`下一篇文章`。等价于`get_data(type=context_doc.type, date_end=context_doc.date, sort='desc', limit=1, page=1)`

### pre_one
类似 next_one，等价于`get_data(type=context_doc.type, date_start=context_doc.date, sort='asc',limit=1, page=1)`

### categories
站点目录，2层之内的日志的分类（文件夹）。
等价于`get_data(type='folder', level=[1,2], min_posts_count=1)`

### cateogry
当URL路径为`/any_thing/<folder-path>`的时候，会去尝试获得一个对应的folder。**注意，此时的<folder-path>必须要以/结尾**
等价于`get_doc(path=get_request_path(offset=1))`

### folder
等价于`category`

### tags
等价于`get_request_path(offset=1).split('+')`
如果访问`/any_thing/tag1+tag2`, 再调用{{ tags }}的时候，获得的是`['tag1', 'tag2']`。

### posts
第一种情况: 当使用了`category`并且有数据的时候，会获得当前目录下的post集合；相当于`get_data(type='post', path=category.path)`;

第二种情况：当使用了`tags`并且有数据的时候，会获得对应tags下的post集合；相当于`get_data(type='post', tags=tags)`;

第三种情况: 当访问`/feed`的时候，会输出10条posts，相当于`get_data(type='post', page=1, limit=10)`；

第四种情况: 当通过`anything/?s=keywords`访问的时候，则会获得关键词对应的post集合，相当于`type='post', keywords=request.args.get('s'))`；

最后一种情况：如果之前的都无匹配，则会返回整站得post集合，相当于`get_data(type='post')`。

### files
第一种情况: 位于某个目录下的，返回其下直接的相册+图片，相当于`get_data(types='folder+image', path=folder.path, min_images_count=1, level=1)`;

第二种情况：返回整站根目录下的相册+图片，相当于`get_data(types='folder+image', min_images_count=1, level=1)`。

### images
第一种情况: 位于某个目录下的，返回其下直接的图片，相当于`get_data(types='image', path=folder.path, level=1`；

第二种情况：返回整站的所有图片，相当于`get_data(types='image')`。

### albums
第一种情况: 位于某个目录下的，返回其下直接的相册，相当于`get_data(types='folder', path=folder.path, level=1, min_images_count=1`；

第二种情况：返回整站根目录下2级目录内的前100个相册并自动分页，相当于`get_data(types='folder', min_images_count=1, level=[1,2], limit=100)`

### post
获得对应url_path的post，并且可以进行评论、显示源文档、记录访问数等功能。

相当于`{% set post = get_data(type='post', url_path=get_request_path(offset=1), limit=1, page=1) %}`, 然后再`{{ add_doc_actions(post) }}`

### file
获得对应路径的文档，可能是file/image/folder的其中一种类型，并且可以进行评论、记录访问数的功能。

相当于`{% set post = get_doc(path=get_request_path(offset=1)%}`, 然后再`{{ add_doc_actions(post) }}`。


### paginator

相当于`get_paginator(0)`，如果你感觉取得的结果不是预期的，建议使用原生的函数`get_paginator`。

比如在首页中:
```jinja2
{% for post in posts %}
    {{post.title}}
{% endfor %}
{{ paginator.page }} {# 这时paginator是有效的#}

{% for post in get_data(type='post') %}
    {{post.title}}
{% endfor %}
{{ paginator.page }} {# 这时paginator是无效的，它先于get_data这个函数执行了#}
{# get_paginator(0).page 这时，这样的写法是有效的。#}
```


### pager
同paginator

### comments
相当于`get_comments()`


## Javascript变量

在启用AutoReload模式，或其它特殊场景下，页面会被嵌入个别FarBox提供的js脚本。
以下全局的变量，需要避免与你自己的脚本冲突，另外，也可以通过设定这些变量，来改变嵌入js脚本的行为。比如声明`no_reload`, 就不会产生任何页面的刷新，即使同步过来的内容发生了变更。

- no_reload: 不允许刷新
- no_reload_for_posts: 不允许post的变动导致的刷新
- fb_current_path: 静态文件的path
- fb_current_doc_path: 当前上下文对象的path
- fb_site_is_live: 正在运行AutoReload
- site_id: 当前网站id
- account_id: 当前网站所属的account_id



