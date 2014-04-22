Title: 数据类别与变量属性
url: data-types
toc: yes


# 数据类别

## site

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

### 属性列表
| 变量名 | 类型 |描述 |
| ----- | --- | --- |
| form | 字典 | 通过POST\PUT方式的传值|
| args | 字典 | 通过GET方式的传值 |
| values | 字典 | 合并了form & args |
| method | 字符串 | 如`GET` `POST` `PUT`等 |
| json | 字典 | 客户端发送的mimetype是`application/json`，则此变量存在 |
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



## Doc

> **Doc是指在你站点目录内的各种文件资源，包括文章、图片、CSS/JS文件、文件夹等。**

### 基础属性

| 变量名 | 描述 |
| ----- | --- |
| path | 相对于站点目录的路径，全小写 |
| full_path | 文档的完整路径, 保留大小写|
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



##  comment

### 属性列表

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



## paginator

### 属性列表
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

# 变量、属性的格式化

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



