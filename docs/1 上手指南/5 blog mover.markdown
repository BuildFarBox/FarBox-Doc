Title: 博客搬家
url: blog-mover


## 怎么从Octopress、Jekyll搬家到FarBox？
直接把日志、附件拷贝（不包含源码，不然网站会乱掉的……）到你自己FarBox的某个站点目录下就可以了。


## 如何从WordPress搬家到FarBox?

如果你使用Chrome/FireFox等现代浏览器，访问自己的网站`http://yoursitedomain.com/?action=import`，然后拖入WordPress的备份XML文档就可以了。

还可以试试[《WordPress to MarkDown》](http://wp2md.farbox.com)这个在线的工具。

这个工具先将你原来博客的存档文件（.xml后缀的文件）转为化MarkDown格式的普通文本文件集合的压缩包；然后将压缩包解压，里面的文件再拷贝到你自己FarBox的某个站点目录下就可以了。

另外，这个工具是开源项目，源代码可以从[<此处>](https://github.com/hepochen/wordpress-to-markdown)获取。它是纯Javascript实现的。


### 百度、网易、新浪、QQ空间、点点等博客如何搬家？

先使用[《Blogs to Wordpress》](https://code.google.com/p/blogs-to-wordpress/)工具，获得转成Wordpress的存档。
*注: 此项目不由我们团队维护。*

再参见上条，使用[《WordPress to MarkDown》](http://wp2md.farbox.com)。

最后把获得的zip文件解压，并拷贝到你自己FarBox的某个站点目录下就可以了。

- - - - - - -

### 怎么从FarBox搬家出去？

你在FarBox上的数据（包括文章、评论、图片等）都在自己的电脑里。

呃，客官，要自己想想办法了，我们也不知道怎么办了……

