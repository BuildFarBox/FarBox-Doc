Title: 获取数据
url: get-data
toc: yes


# 获取文档

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

## 文档属性

> **`文档`/Doc是指在你站点目录内的各种文件资源，包括文章、图片、CSS/JS文件、文件夹等。**
> get_data一般会返回文档对象的列表，而get_doc/get_connected_one则返回一个文档对象。

### 基础属性

| 变量名 | 描述 |
| ----- | --- |
| path | 相对于站点目录的路径，全小写 |
| full_path | 文档的完整路径, 全小写|
| raw_path | 文档的完整路径, 保留大小写|
| parent_path | path的父目录路径 |
| parent_full_path | full_path的父目录路径 |
| parent_raw_path | raw_path的父目录路径 |
| type | 文档类型，有folder/post/image/file四种 |
| title | 标题 |
| date | 文档的时间，比如可作为文章的发表时间 |
| m_date | 文档最后修改时间，评论文章也会导致m_date变动 |
| raw_date| 文档的最后修改时间 |
| position | 用户自定义的位置, 浮点数类型 |
| size | 文档的大小 |
| content_type | 内容类型，比如`image/jpeg` |
| visits | 页面访问数 |
| _visits | 在不改变文件路径的前提下，约等于访问IP数 |
| content | 如果没有特殊说明，是文档的源数据 |
| ext | 文件后缀名，比如`jpg` `txt` |

### post类型专有属性

> post类型，即文件后缀名为.txt .mk .md .markdown的文档。

| 变量名 | 描述 |
| ----- | --- |
| content | HTML格式的正文 |
| toc | TableOfContens, HTML格式|
| status | 文章的属性，默认为public |
| cover | 文章封面, 从内容中提取出来的第一张图片 |
| raw_content | 纯文本格式的正文，不包含metadata |
| _content | 原始正文，包含metadata信息 |
| metadata | post的扩展属性 |
| tags | 标签， 列表类型|
| url_path | 自定义的url |
| url | '/post/’ + url_path|
| comments_count | 评论数 |
| posts_count | 恒等于1 |

补充说明:
- 如果用户没有自定义url_path，则系统会根据其文件路径，自动生成一个url_path
- 正文中如果没有图片，则cover为None
- url只是一种约定的规则，如果负责处理`文章详细页`的不是`template/post.html`，则这个url是无效的，需要自己根据url_path组装。
- 在调用content的时候，如果没有设置使用toc，那么，正文内的字符串`[TOC]`会被toc内容替换。

### image类型专有属性

| 变量名 | 描述 |
| ----- | --- |
| url  | 图片的网址 |
| image_width | 图片宽 |
| image_height | 图片高 |
| exif | 图片的exif信息，对象/字典类型 |
| comments_count | 评论数 |
| images_count| 恒等于1|



#### image. exif属性

| 变量名 | 描述 |
| ----- | --- |
| make | 设备生产商，如 Apple |
| model | 设备型号， 如 iPhone 4 |
| datetime | 照片拍摄时间， 如 2012:10:17 18:22:14 |
| fn | 光圈值 |
| flash | 曝光模式，整数 |
| focal_length | 焦距 |
| exposure | 曝光（快门）时间 |
| iso | ISO值 |
| program | 拍摄模式，0-8 |
| latitud | 纬度，北纬为正、南纬为负数|
| longitude | 经度，东经为正、西经为负数|
| altitude | 海拔高度 |
| height | 拍摄原图高度|
| width | 拍摄原图宽 |


#### folder类型专有属性

| 变量名 | 描述 |
| ----- | --- |
| content | HTML格式的正文 |
| raw_content | 纯文本格式的正文，不包含metadata |
| _content | 原始正文，包含metadata信息 |
| metadata | 扩展字段 |
| posts_count| 包含的文章数(含子目录) |
| images_count| 包含的图片数(含子目录) |
| _posts_count| 包含的文章数(不含子目录) |
| _images_count| 包含的图片数(不含子目录) |

> content/raw_content/_content/metadata的属性来自于文件夹下`index.txt`这样的文档，如果有的话。


# 其它相关数据

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


### paginator属性列表
| 变量名 | 描述 |
| ----- | --- |
| per_page | 每页数量 |
| object_list | 当前页的对象列表，比如`posts` `files`，不常用 |
| total_count | 查询对象的总数 |
| total_pages | 总共多少页 |
| page | 当前是第几页 |
| previous_page | 上一页页码 |
| pre_page | 同previous_page |
| previous_page_url | 上一页的url |
| pre_page_url | 同previous_page_url |
| next_page | 下一页页码 |
| next_page_url| 下一页的url |
| has_next| 是否有下一页 True/False |
| has_previous | 是否有上一页 True/False |  
| has_pre | 同has_previous |  

### 代码示例  

一个比较常见的，对paginator处理的代码。

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


## get_comments

接收参数`<parent>` `<limit>` `<sort>`.
如果parent为空，则会获得当前站点下的所有评论。
通常在文章详细页面中，使用`post`来做parent，从而获得当前文章的评论列表。
代码示例:
```
    {% set comments = get_comments(post) %}
```

### comment属性列表

| 变量名 | 描述 |
| ----- | --- |
| content | 评论内容，已经HTML格式化 |
| raw_content | 评论内容，未格式化 |
| author | 评论者称呼  |
| site | 评论者网站地址 |
| date | 评论日期  |
| email_md5 | Email地址对应的md5值 |
| ip | 如果有的话，表示评论者的IP |

> comment.email_md5可用于取头像，比如 http://www.gravatar.com/avatar/{{ comment.email_md5 }}


### is_comment_allowed
接受一个doc参数，即当前的doc是否允许评论。一般会传入context_doc，比如`{{ is_comment_allowed(context_doc) }}`。

context_doc，是由add_doc_actions运行产生的。


# 默认变量

## site
>  当前被访问的网站。

### 属性列表
| 变量名 | 描述 |
| ----- | --- |
| _id | site_id |
| title | 网站的标题 |
| site_path | 字符全部小写，`/site_folder_name/` |
| domains | 网站的域名（多个）， 数组型 |
| tags | 当前网站(public)日志的tags，数据类型为数组，tag_name+tag_count |
| avatar | 网站所有者的头像地址 |
| configs | 网站的自定义属性 |
| content | 网站的描述(HTML) |
| raw_content | 网站的描述(TXT) |
| template_key | 网站模板包的key值 |
| owner_template_key| 网站自行打包的模板包的key值 |
| config_path | 当前生效的网站配置文件，比如site.txt |
| comment_script | 网站目录下comment_js.txt(md/mk/markdown)的内容 |

### site.tags

1，site.tags是按照标签对应的文章数，倒序输出，形如:
```
[('tag 1',3), ('tag 2', 1)]
```
2，site.tags模板代码示例:
```
{% for tag_name, tag_count in site.tags %}
{{ tag_name }}, {{ tag_count }} </br>
{% endfor %}
```
3，site.tags的数据每1小时会被被缓存一次。

### site.title

默认值是你网站目录的名称, 或等价于`site.configs.title`。


## request

> 当前用户的（浏览器）请求信息。

### 属性列表
| 变量名 | 类型 |描述 |
| ----- | --- | --- |
| form | 字典 | 通过POST\PUT方式的传值|
| args | 字典 | 通过GET方式的传值 |
| values | 字典 | 合并了form & args |
| method | 字符串 | 如`GET` `POST` `PUT`等 |
| json | 字典 | 客户端发送的mimetype是`application/json`，则此变量存在 |
| xml | 字典 | 跟json字段功能类似 |
| is_login | 布尔值 | 当前用户是否已登录 |
| is_admin | 布尔值 | 当前用户是否是站长 |
| language | 小写字符串 | 当前访客浏览器的语言，比如`zh_cn` |
| path | 字符串| - |
| url | 字符串 | - |
| base_url | 字符串 | - |
| url_root | 字符串 | - |
| url_without_host | 字符串 | - |


**注意，is_login/is_admin也可以在客户端的Cookie中获取，但Cookie中仅能作为标记符，如果涉及读、写权限的操作，服务器有自己的session进行判断。**

### request.path

假设`http://www.example.com/page.html?x=y` 被访问，则request有以下几个变量。

| 变量名 | 值 |
| --- | --- |
| path | /page.html |
| base_url	| http://www.example.com/page.html |
| url	| http://www.example.com/page.html?x=y |
| url_root | http://www.example.com/ |
| request.url_without_host |/page.html?x=y |

## 常用变量
> 1, 常用变量是自动套用get_data函数获得的数据，如果页面的逻辑比较复杂，我们建议直接使用get_data来获取数据，从而避免不必要的冲突。
> 2, 一般情况下，不要直接使用`常用变量`作为if/else的判断逻辑，比如 {% if posts %}这个判断，如果posts不是自己通过get_data获取的，那么基本上都是有返回值的。

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



## 其它变量

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

# 特殊属性与格式化

一些字段、属性在页面呈现的时候，需要不同的格式支持。为了让API调用更加人性化，我们对一些常见的属性类型进行了重新封装。

## Date

直接使用(比如`{{post.date}}`)，默认返回`2012-12-22 10:11`的格式。

### format

指定TimeFormate，进而格式化日期的`函数`。

代码示例:
```
    {{ post.date.format('%Y-%m-%d') }}
   # 最终格式为 2012-12-22
```

### before

接受一个整数参数，表示?秒之前的时间，返回一个新的Date对象。

### after

同before，表示?秒之后的时间。

### TimeFormat
	
```
%a 本地的星期缩写
%A 本地的星期全称
%b 本地的月份缩写
%B 本地的月份全称
%c 本地的合适的日期和时间表示形式
%d 月份中的第几天，类型为decimal number（10进制数字），范围[01,31]
%f 微秒，类型为decimal number，范围[0,999999]，Python 2.6新增
%H 小时（24进制），类型为decimal number，范围[00,23]
%I 小时（12进制），类型为decimal number，范围[01,12]
%j 一年中的第几天，类型为decimal number，范围[001,366]
%m 月份，类型为decimal number，范围[01,12]
%M 分钟，类型为decimal number，范围[00,59]
%p 本地的上午或下午的表示（AM或PM），只当设置为%I（12进制）时才有效
%S 秒钟，类型为decimal number，范围[00,61]（60和61是为了处理闰秒）
%U 一年中的第几周（以星期日为一周的开始），类型为decimal number，范围[00,53]
%w 星期，类型为decimal number，范围[0,6]，0为星期日
%W 一年中的第几周（以星期一为一周的开始），类型为decimal number，范围[00,53]
%x 本地的合适的日期表示形式
%X 本地的合适的时间表示形式
%y 去掉世纪的年份数，类型为decimal number，范围[00,99]
%Y 带有世纪的年份数，类型为decimal number
%Z 时区名字（不存在时区时为空）
%% 代表转义的"%"字符
```

## Text

> 目前仅仅对`content`字段生效。

### plain

输出仅有换行格式的平文本。但如果原文有HTML标签，并不会转义。

### safe_plain

同plain这个属性，但是会对HTML标签进行转义。

### without_toc

针对post类型，内容不包括toc部分。

### opening

如果`content`中有`<!--more-->`(单独一行)的话，则会返回被它分割后的第一部分。

如果你在写日志是，特别进行了这个标注，则需要调用`{{ post.content.opening }}`。

如果没有这个标注的话，则返回空文本。



### limit

limit函数可截取内容的前面部分，接受两个参数，length表示截取的长度，with_mark则表示如果截取的部分并不是全文，则默认追加在后面的字符串，默认为`......`。

同时，返回的结果，还有两个属性，一个为more，表示是否还有更多的内容；一个为`no_pic`，即将内容中的img标签去除。

```

{% set content = post.content.limit(100, '...')  %}
	<div> {{ content }} </div>
{% if content.more %}
	<a href="#">Read More</a>
{% endif %}

```

### 其它

```
post.content[0:100] # 正文的前100字，纯文本，非HTML格式。
post.content(100) # 正文前100字，HTML格式。

```

## ImageURL

仅仅针对image类型的url属性。

`image.url`可以自动处理图片的尺寸，格式为`image.url.s<width>x<height>`或`image.url.s<width>x<height>xfixed`。

另外`image.url.s<参数字符>`等价于`image.url['<参数字符>']`。

举例说明:

```
{{ image.url.s50x50xfixed }} 返回50像素的正方形缩略图， 等价于{{ image.url['50x50xfixed']}};

50x50, 则只是等比例缩小，最宽&最高不超过50像素;

0x50，则最高不超过50像素;

0x50xfixed，则高度始终为50像素

其它依次类推。

```

另外，比较特殊的一个取值是`{{ image.url.blur }}`则会返回进行了模糊处理的特效图。


由于图片的处理会消耗比较大的计算资源，FarBox会自动处理图片的边界，比如width=550，我们最终会转化为临近的640.

| 参数 | 说明 | 最终转化的可能值 |
| --- |--- | --- |
| width | 图片的宽 | [40, 80, 160, 214, 320, 428, 640, 960, 1280] |
| height| 图片的高 | [40, 80, 160, 214, 320, 428, 640, 960, 1280] |


FarBox默认返回的图片尺寸最大不超过`1280*1280`，当原图尺寸超过这个的时候，可以请求 `image.url.large`，这样会返回一张最大不超过`1560*2560`的大图。



## List

下列函数，只有对象类型是列表的时候，才会生效。另外，下列的函数，基本都是一次甚至多次遍历，如果在一个页面内数据较多并且多次执行，可能会导致页面超时，而渲染失败。

### sort_by
对数组进行重新的排序，仅接受一个参数`attr`(根据某个属性排序)，如果attr前面以`-`开头，则表示结果倒序。

```
{{ posts.sort_by('position') }}  # 按照自定义的位置排序
{{ posts.sort_by('-position') }} # 按照自定义的位置倒序
{{ posts.sort_by('date.year') }} # 按照日志的发表年份排序

```

### group

对列表进行重新归档，仅接受一个参数`attr`(根据某个属性排序)，如果attr前面以`-`开头，则表示结果倒序。

如果是多个子属性的混合，可以使用`attr:sub_att1+sub_attr2`, 比如`posts.group('date:year+month')`；如果子属性是一个列表型的值，则会对列表里的每个值进行归档，比如`posts.group('tags')`。

归档的返回值也是一个列表，每个元素的形式为：[group_key, [item1, item2, item3, .etc]]。

- - - - - - -

代码示例，（默认模板中）我们可以对一个日志列表按照Tags或年份进行归档。

```html
{% set posts = get_data(type='post', limit=300, sort='desc') %} 
{% if request.args.use == 'tags' %}
    {% set entries = posts.group('tags') %}
{% else %}
    {% set entries = posts.group( 'date:year') %}
{% endif %}
```

最终获得的`entries`形如：
```
[
    (2013, [<post1>, <post2>, <post3>]),
    (2012, [<post1>, <post2>]),
]
```  

通过以下方式进行页面的呈现。
```
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


**注意：归档的倒序，只是组与组之间的，比如`2013年`这个组可以排在`2012年`之前，但是组内的元素次序，仍然是原来的次序。**


### filter

用来过滤一个列表性质的数据。接受两个参数，一个为attr, 一个为attr_value, 判断的依据是数据对象的属性(attr)完全等于指定值(attr_value)；如果attr_value是一个列表，那么`对象的属性值在这个列表内的`讲作为判断依据。

比如objects = [{value:1}, {value:2}, {value:1}]

那么objects.filter('value', 2) 将会返回[{value:2}]。

### pick

```json
objects = [
    {path: "1"},
    {path: "2"},
    {title: "hello"}
]
```

这时`objects.pick('path')` 会返回`['1', '2']`; 即从列表的对象中取特定字段，形成新的列表。

### add

`add(obj, position=-1)`, 可以往列表中添加一个对象，默认的position=-1表示于最后的位置添加。

add这个命令会尽可能避免新元素（如果是一个数据对象）与旧列表的冲突。如下例子:

```
posts = [<p1>, <p2>, <p3>]
new_post = <p2>
posts.add(new_post, 0)
最终的posts = [<p2>, <p1>, <p3>]
```



## json

如果对象的类型是数组或者字典，那么，可以直接使用其json属性，将原对象转为json格式。如果转化失败，则返回原对象。

代码示例:

```
{% set posts = get_data(type='post', limit=10) %}
{{ posts.json }}
```



