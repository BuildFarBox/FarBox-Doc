title: 日志的属性
url: post-configs

日志的配置属性，可以通过`{{ doc.metadata }}`获取。

**特别说明: 如果日志的文件名为'index.txt', 则这个数据对象将会直接转移到其所在的目录, 作为扩展属性。**

比如， `folder/index.txt`的内容如下:

```
title: folder_title
position: 1
date: 2013-12-11

description here
```

那么`folder/`这个目录的`{{doc.title}}` `{{ doc.position}}` `{{doc.date}}`则实际上是index.txt中进行申明的。



### title
如果有定义title，则会覆盖写入至对应的`{{doc.title}}`字段。

如果没有定义title，则会自动取文件名作为日志的标题； 如果文章中有，且仅有一个H1的一级标题，则会以之作为title。


### date

日志的发布时间，如果没有声明，我们会按照以下的规则获取文章的发表时间:

1. `2012-12-12 Title.txt`这样的文件名，我们会取出前面符合日期规则的字符作为发表时间;
2.  默认取文件的最后修改时间作为发表时间。


### position
这个字段，可以作为排序的依据，要配合`get_data`这类的函数使用，并声明`sort='position'`。

除了在metada中对position进行支持之外，文档的原文件名也会为position提供支持，比如`1.2 my article.txt`或者`1.3 my other tiltes`, 分别获得的position为`1.2` `1.3`，其中` `(空格符)用以分割position与正常的标题。

注意，position仅支持正序，即`1.2`会排在`1.3`之前。

### status
默认为public，也是默认显示在网站中的日志类型。

它的value可以是任意值，但不能超过20个字符长。


### toc

一篇日志如果使用多级标题（H1、H2、H3 .etc)，那么'post.toc'这个属性则对应多级标题的索引，即Table of Contents。

单篇文章的`toc`配置信息, 优先级高于站点上的配置。

接受值: yes/no


### url
可以用来自定义日志的URL

它会写入到{{doc.url_path}}这个字段中去。

### slot1

特殊的扩展属性，全文搜索的时候，可以指定对这个字段进行搜索。

### slot2

特殊的扩展属性，全文搜索的时候，可以指定以这个字段进行排序。


