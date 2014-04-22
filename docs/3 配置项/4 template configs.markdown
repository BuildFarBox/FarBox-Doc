title: 模板包的配置
url: template-configs


## 模板包（Template Package）的概念
`Template Package`是指对一个站点内所有模板进行的归档。那么，当模板作者更新了模板(某个源文件)的时候，使用者就会实时使用最新的模板。

### readme文档
`Template Package`是通过侦测`template/readme.txt`这个文件的变动来实现的。

一个简单的readme.txt示例：
```
key: farbox-template
title: 我的模板标题
domain: http://mysite.com

这个模板是一种对简洁的探索。
```

**key这个字段是必须声明的，其它的则是作者随意；但必须需要注意的是，readme的所有信息，都是公开可以被访问到的。**

当readme.txt发生变更的时候，打包系统会根据作者的uid+key生成一个唯一的template_key，来作为template_package的唯一标识码。




