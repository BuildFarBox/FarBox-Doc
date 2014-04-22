Title: 网站的配置
Url: site-configs
toc: yes
---

网站的配置是通过`site.txt`这个文件实现的。一般情况下，我们可通过yoursite.domain.com/admin对配置进行管理，本质上是更改了`site.txt`的文件内容。

网站的配置信息, 在自定义模板的API中通过`{{ site.configs }}`获取。

一般情况下，`{{ site.configs }}`是松散的，根据具体的情形按需配置即可；但有些特殊的字段(属性)是系统默认会进行处理。

## 域名

### domain
绑定当前网站的域名，如果是独立域名，则需要修改这个域名的DNS记录，具体请参考[此处](domain)。


### domains
效果同domain字段，但是一个列表的形式，最多不超过10个。

如果是farbox.com的子域名形式，需要注意: 2位以内的二级域名是被保留的；另外，如果无端占用多个不使用的域名，会被系统自动回收。

参考如下:
```
---
domains:
  domain1.com
  domain2.com
---
site description
```

## 内容显示

### posts_per_page

每页中日志的数量。但这个并不是严格意义能生效的。

比如在模板中写`get_data(type='post')`这样获取的日志采用的分页会默认使用site.configs.posts_per_page

如果是写成`get_data(type='post', limit=10)`, 则这个配置项就无效了。


### toc

一篇日志如果使用多级标题（H1、H2、H3 .etc)，那么'post.toc'这个属性则对应多级标题的索引，即Table of Contents。

在站点配置中，如果toc为`yes`的话，则标示调用`{{ post.content }}`的时候，默认会显示`{{ post.toc }}`。

接受值: yes/no，默认为no

### post_content_type

当调用`{{ post.content }}`时候生效，如果是`plain`，则渲染普通的文本，而不是Markdown的格式；其它值，则会按照Markdown的方式表现出来。

接受值: plain/markdown，默认为markdown


### post_paragraph_indent

如果为yes，则会在HTML的页面渲染过程中，插入`<style type="text/css">.post p{text-indent:2em}</style>`来实现首行缩进2字的效果。但最终的效果，会受文章本身以及HTML结构的影响。

如果为hard，则表示硬分行，即每行都会进行缩进。按照CSS规则，默认的缩进，是作用在P标签上的，普通的换行`<br>`是无效的。如果设定值为`hard`, 模板系统会自动重组HTML结构，使得缩进可以作用于每一行。

接受值: yes/no/hard，默认为no


### mathjax

如果为yes，则在HTML渲染的过程中，插入mathjax的脚本，以实现数学公式的渲染。

接受值: yes/no，默认为no


### file_path
如果在查找图片、相册的时候，并且没有指定path的时候，它会生效，作为默认的path参数传入。


## 数据与安全

### comment

网站是否允许评论。默认为可评论。

接受值: yes/no，默认为yes

### sync_by_3rd
允许第三方用户直接推送内容到你网站的`share`目录下。

接受值: yes/no，默认为no

### sync_by_3rd_public

在`sync_by_3rd`开启的前提下，允许非登录用户（即匿名用户）推送数据。

接受值: yes/no，默认为no


### users

增加访客密码。`username@password`的格式，每行一条。

因为FarBox并不明文保存密码，所以，加密成功后的记录，会表现为`username@xxxxxxxxxxxxxxxxxxxxx`，这也是有效的记录。



### score_degree

这是数据dig系统的敏感值。计数单位可以简单的理解为`天`，一般建议为0.5，这样可以让一天之内的最新&最热数据排在最前面。

数据的dig系统默认都处于运行状态，如果需要输出经过其排序的数据，则要在获取数据的函数中申明sort='_score'即可。



##模板引擎


### scripts_per_page

一般的谷歌统计代码、第三方评论代码可以写入到这个字段。它会在渲染HTML页面的时候，将里面的内容插入到`</body></html>`之前。

如果模板页面中有`<!--scripts_per_page-->`的标记，则是插入到这个指定位置。


### scripts_for_doc

效果同`scripts_per_page`，但是仅仅在使用过`add_doc_actions`时生效。一般比如是文章的详细页。

如果模板页面中有`<!--scripts_for_doc-->`的标记，则是插入到这个指定位置。


### template_priority

模板如果有自定义与其它克隆方式共存的时候，可以通过调整模板优先级进行切换。

| 参数名 | 说明 |
| ---- | ---- |
| auto | 自动，默认值 |
| self | 自定义模板优先 |
| default | 特殊参数，表示强制使用默认模板 |

- self，即自定义模板优先，它需要有自定义模板资源的时候才会生效；反之，等同于`auto`
- auto，先查询是否有模板包继承（或克隆）；如果没有，则会寻找自定义模板资源；都没有，最终使用默认模板。


### template_inherit

FarBox有最基础的默认模板，当某个页面没有被自定义过的时候，那么它会从基础模板中继承，而不是引发404页面。如果设为no，则不再从基础模板中继承。

继承基础的默认模板，仅针对`主页面`，比如`post.html`这样可以通过URL访问的。其它的，比如`include "include/comments.html"`, 如果自定义模板中没有对应的资源，仍然会去寻找默认模板中的资源，进行继承。

接受值: yes/no，默认为yes

### template_clone_allowed
是否允许别人克隆你的模板。如果你的网站内有自定义的模板，这个设置项才会生效。

接受值: yes/no，默认为yes.

### autoreload
会在页面的最后，插入`/template/js/autoreload.js`, 从而实现AutoReload的功能。

接受值: yes/no/admin/auto， 默认为auto。

`auto`: 网站模板变更10分钟内，或者当前为站长；生效。
`admin`： 当前为站长时，生效。
`no`: 禁止插入autoreload.js这个脚本。
`yes`: 总是生效。

## 其它功能

### nano
是否加入Nano Network进行网站的加速。 Nano Network的节点状态: <http://status.farbox.org/service/nano-network>

注意: **如果你正在修订模板，并且页面处于AutoReload模式，请不要启用Nano Network，它可能会慢半拍。**

接受值: yes/no，默认为no.


