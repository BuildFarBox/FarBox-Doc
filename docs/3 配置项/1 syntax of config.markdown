Title: 配置语法
url: syntax-of-configs


## 什么是配置项

`配置项`是指通过文本文件的头部内容，来扩展文档本身附加信息。

支持的类型包括：网站、日志、文件夹、模板包。

**我们支持.md .markdown .mk .txt这些后缀的文本文件，后续中如果说是针对`site.txt`的，则意味着其它同名的后缀(比如site.md)也是等效的。**



## 定义配置项的语法

`配置项`定义，需要在文本文档的正文之前，也就是**头部**；有两种定义的方式。

方式1：每行用`key: value`的形式

```
title: 我是标题
url: this-is-url

这里是正文
```

则获得的配置信息为

```json
	{
		"title": "我是标题",
		"url": "this-is-url"
	}
```

- - - - - -


方式2：使用YAML格式，但需要用---包裹
```
---
key: value
title: Blogging in FarBox
---

这里是正文
```

则获得的配置信息为

```json
	{
		"key": "value",
		"title": "Blogging in FarBox"
	}
```


### 注意事项
1. 两种方式只能选择一种，两者无法同时生效。
- key的值必须是英文，并且不能以数字开头
- key不能以数字为开头，key的文字之间不能使用运算符号，比如`+-x/`，`post-x: value`这种写法是错误的，正确的是`post_x: value`
- key不区分大小写，`KEY`与`key`的效果是一样的



