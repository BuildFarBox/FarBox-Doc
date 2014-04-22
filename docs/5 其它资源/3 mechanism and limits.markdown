Title: 工作机制与限制
url: mechanism-and-limits
Date: 2014-02-10 12:59:33


## 文件支持与限制

### FarBox支持的文件类型

| 类型 | 后缀名 | 
| --- | ---- |
| 图片 | bmp gif jpeg png pjpeg tiff |
| 前端资源 | css html htm js json ico xml swf less sass scss |
| 压缩文件 | zip tar gz rar 7z |
| 文档 | pdf txt config cvs |
| 字体 | woff ttf otf eot |

### 限制
| 类型 | 限制说明 |
| --- | ---- |
| 隐藏文件 | 不处理，包括路径中有`/.`或者以`~`结尾的|
| 最大文件尺寸 | 15Mb，超出不处理 |
| 日志类型 | 不超过3Mb，超过不处理; 且仅截取前50万字节内容 |
| HTML类型文件 | 不超过3Mb，超过则不处理 |
| template内资源 | 不超过500k，超过则不处理 |
| template内文件夹 | 不处理 |
| tempalte内资源数量 | 不超过200，超过则不处理 |

*HTML类型文件包括以下后缀: html htm css js json swf xml less sass scss*

## 同步机制
### 与Dropbox同步
1. FarBox与Dropbox保持实时的同步。
2. 为避免疏漏，我们另外会保持10分钟一次的主动同步。

### FarBox自身的同步
通过FarBox API保存的数据，如果是日志，或者模板内的文件，会使用FarBox自身系统的实时系统，推送到对应网站上，进而实现页面重新渲染或者刷新。

使用FarBox API的，比如`FarBox Editor`， `http://yourdomain.farbox.com/admin/code`这个在线编辑器。



