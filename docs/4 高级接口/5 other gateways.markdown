title: 其它接口
url: other-gatewys


## 网站与账户状态

URL地址： `<yourdomain.com>/service/status`

### Account Status

- Cloud Service: 第三方云存储提供商，现在仅支持Dropbox
- Token Status: 第三方云存储的授权状态，如果不是`Normal`， 则需重新授权
- Last Synced at: 最后一次同步的时间
- Synced Times: 同步总次数
- Last Changed at: 最后一次文件有变化的同步时间
- Changed Times: 文件有变化的同步总次数
- TimeZone: 当前账户所在的时区


### Site Status

- Site ID: 是由当前账户ID与网站目录组成的key
- Last Updated: 当前网站内的最后修改时间
- Template Priortiy: 模板的优先级


## 同步日志
 
URL地址： `<yourdomain.com>/service/sync_logs`

这里会显示当前站点对应账户的所有同步日志。

每一个文件的同步情况，最多保存最近的500条。


## Avatar

URL地址：`/service/static_3rd/avatar/<email_md5>`

我们对https://www.gravatar.com/做了一次代理，可以更加快速的获取头像，也避免通常情况下HTTPS中调用HTTP浏览器产生的警告。

## 免费图库

URL地址: `/farbox_free_image.jpg`， 会随机返回一张版权不限定的图片。

**这个URL跟普通的图片调用方式一致**， 比如`/farbox_free_image.jpg?width=120&height=120&fixed=true`返回的是120x120固定尺寸的缩略图。

## HTTP Response Headers

> FarBox渲染过的页面都会往页面的Headers中填充一些信息以作标识

| Name | 说明  |
| ----- | ---- | 
| x-cached | yes/no, 如果为yes则是从服务器内存中直接读取，速度最快 |
| x-cache-key | 中心服务器中的内存地址标识值 |
| x-render-time | 页面渲染的时间(单位秒)|
| x-route | 渲染模板的函数名 |
| x-template-Path | 使用的模板的路径名 |
| x-nano-hit | 是否命中nano加速网络 |
| x-nano-time | nano网络中消耗的时间, 并以此为准|


## 第三方静态资源

URL地址: `/service/static_3rd/staticfile/<file_path>`

这是对`http://cdnjs.cloudflare.com`做了一个直接的代理，比如以下两个源码是一致的:

1, /service/static_3rd/staticfile/`ajax/libs/font-awesome/4.0.3/css/font-awesome.min.css`
2, http://cdnjs.cloudflare.com/`ajax/libs/font-awesome/4.0.3/css/font-awesome.min.css`



