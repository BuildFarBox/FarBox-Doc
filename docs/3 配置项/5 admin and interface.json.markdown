Title: 后台管理的配置
URL: admin-configs
Date: 2014-02-02 13:36:11
toc: yes

## 后台管理

我们可以通过配置[site.txt](site-configs#item-3-2)来设置网站。

FarBox的默认模板中通过`admin/index.html`这个template文件实现的后台管理（URL为`/admin`）。

其实现的基本逻辑如下：

1. 获取当前用户/网站的数据（由`admin/data.html`这个template文件负责）
2. 按照interface.json的规则，构造一个后台管理的界面
3. 点击保存的时候，将各个配置属性转化为`site.txt`的文本内容，并通过FarBox API重新写入Dropbox中。

> [源代码参考此处](https://github.com/BuildFarBox/FarBox-Admin/tree/master/admin)，index.html负责HTML页面，data.html负责初始数据的载入，dash目录是css/js资源。

## interface.json

> 默认的interface.json[源码](https://github.com/BuildFarBox/default-template/blob/master/template/interface.json)

### 基本结构

![Image Title](/_image/configs/interface.png)

#### level-1: pages， 类型为[]
pages会生成一个标签页的组合，可以点击切换的Tab。

如果pages中只有一个元素，并且这个元素内有设置`extends: true`, 那么，pages后面的几个要素（通常是比较复杂的设置项），会从FarBox默认的interface.json中继承。

#### level-2: page
- title，标签页的标题
- groups，标签页内各个配置的归组, 相当于HTML中的fieldset标签

#### level-3: group
- title: 组的标题
- cells: 组内的再细分

#### level-4: cell
- parts_per_line: 每行显示几个
- parts： 配置项

#### level-5: part
- title: 配置项的名称
- key： 配置项的key值，比如title, 最终可以通过{{ site.configs.title }}调用
- model: text/select/check/textarea/image中的一个值
- default_value: 默认值，如果是text/textarea的话，会被当做placeholder，select/check则会起到选中的作用
- is_list： 对textarea类型的有效，表示重新压制回YAML格式时，这是一个列表，而不是多行文本
- options: 如果是select类型的话，可以写入options
- value: 对`image`类型有效，是一个路径名，比如`/_image/avatar.jpg`, 使用者可以在后台管理中直接拖入图片来替代这个路径对应的文件。

### 关于options
- title: 显示值
- vlaue: 真实的变量值
**一般情况下，options是一个列表，如果options直接写成字符串形式的`root`，按照约定，这会显示一个文件夹结构。**

### 多语言支持

title和options这两个字段默认可以匹配系统语言。
比如
```
{
    "title": "default title",
    "title_zh_cn"： "if browser language is zh-cn, it will work."
}
```
但需要注意，`title_zh_cn`必须是小写的。

## 注意事项
如果你的模板需要调用一些特殊的字段，比如`site.configs.sub_title`类似的，那么通过interface.json的配置，可以快速地自定义后台管理界面，这是非常棒的。

但定制interface.json的时候，如果不清楚整个机制的话，建议只要在默认的interface.json上修改即可。





