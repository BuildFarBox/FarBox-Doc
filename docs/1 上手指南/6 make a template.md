title: 如何自定义模板？
url: how-to-make-a-template


## 第一步，了解模板目录所在的位置

**假设你现在的网站目录为`Dropbox/应用/FarBox/MyBlog/`**

模板页面则要放置于`Dropbox/应用/FarBox/MyBlog/template/`的目录下， 没有这个目录，则需要创建一个。

那么`Dropbox/应用/FarBox/MyBlog/template/index.html`的内容，将会是你的网站首页。


## 第二步，下载网站模板源码

我们建议可以先下载[默认模板的代码](https://github.com/BuildFarBox/default-template)。

也可以到我们团队托管在GitHub的[代码库](https://github.com/BuildFarBox/)中找, 或者直接[搜索](https://github.com/search?o=desc&q=farbox&ref=cmdform&s=updated&type=Repositories)其他人的模板代码。

然后，把需要的模板文件放在自己网站的`template`目录内。

几种常见的位置错误，举例说明：
```
Dropbox/应用/FarBox/MyBlog/template/index.html --> 正确的

如果出现以下路径，则是错误的:
Dropbox/应用/FarBox/MyBlog/template/Name/index.html --> 错误的
Dropbox/应用/FarBox/MyBlog/template/Name/template/index.html --> 错误的
```


## 第三步，了解FarBox模板的基本语法

```jade
head
	if site.title
		title= site.title
body
	for post in posts
		h1= post.title
```

这样的API语法是不是很漂亮? 具体可以了解[模板引擎](template-engine)。


## 实时渲染模板，开始自定义吧！

等模板文件完成了同步，还可以直接使用在线的代码编辑器进行修改，地址为`http://yourdomain.com/admin/code`。

当你修改了一个模板文件后，网站会自动重新载入。在不同的浏览器，不同的设备（电脑、手机、PAD）上，都无需手动刷新，而能实时显示修改了的效果。

