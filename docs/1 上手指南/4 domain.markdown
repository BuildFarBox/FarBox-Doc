Title: 网站域名
url: domain


## 域名相关

### FarBox提供的二级域名

我们提供免费的二级域名, 形式如`***.farbox.com`, 3位长度以内的是系统保留域名。

我们不允许无理由占用二级域名的行为，举个例子，如果你占用了100个FarBox二级域名又没有在实际使用，我们会拒绝再为你提供任何的服务。

*另外需要注意，一些潜在的，拥有明显FarBox品牌特征的，请不要占用，比如blog.farbox.com, wiki.farbox.com, app.farbox.com .etc*

### 域名被访问的机制

当`example.com`被正常访问时，会发生下面两件事情:
step1. `example.com`会被解析并指向FarBox的服务器;
step2. 服务器会寻找`example.com`对应的网站。

如果是`***.farbox.com`这样的二级域名，则不会发生step1；反之则是独立域名，要实现step1，需要修改这个域名的域名解析记录。

step2，则是要通过网站自身的设置来实现。

## 绑定独立域名

### 方式1: 修改NameServer
如果你知道自己域名的DNS是如何管理的，那么请将域名的NameServers(域名注册商处设置，而非DNS托管商)设置为`ns1.farbox.net` `ns2.farbox.net`。在设定NameServer之前，你需要确认该独立域名已经在自己的`网站设置`中进行了绑定。

如果不知道DNS是什么概念，也可以登录网站的后台管理，购买一个域名，我们会自动地帮你完成所有的设置。（*即将推出*）

### 方式2: 修改DNS的CNAME

比如`yourdomain.com`需要绑定的，则在这个域名的DNS中创建一个CNAME记录，指向`yourdomain.com.park.farbox.net`。

在`yourdomain.com.park.farbox.net`中， `yourdomain.com`用来确认你的网站，`park.farbox.net`则是确认FarBox的服务器地址。

### 方式3：修改A记录（不推荐）

> 尽量不要使用方式3，当我们IP发生变化的时候，这个记录就会无效掉。
> 同时，FarBox针对中国各省市的线路优化也将失效。
> 有些DNS托管商，可以设置根域名的CNAME，但会提示跟MX记录冲突；尽管试一下，如果设置后域名访问正常、邮件接收正常，便是可以的。


如果你的根域名无法使用CNAME指向，那么可以直接使用A记录。A记录的IP地址则需要查询`yourdomain.com.park.farbox.net`。 

通常以下两个（操作系统的）命令，能帮你找到对应的IP地址。
```
ping yourdomain.com.park.farbox.net
nslookup yourdomain.com.park.farbox.net
```


### 如何绑定二级独立域名?

假设你只需要绑定`blog.yourdomain.com`，如果是通过NameServer方式绑定的，不需要做其它处理，直接用就可以了。

如果是通过CNAME方式，那么，你需要为这个二级域名设置一个CNAME记录，指向`blog.yourdomain.com.park.farbox.net`即可。

## 常见问题

### 为什么我修改了域名的DNS，但是没有生效？

DNS生效时间，根据你所在的注册商不同，时间有长有短。一般十几分钟，长一点的时间也不会超过24小时。

另外，像`yourdomain.com.park.farbox.net`这个虽然用于绑定，但同样也是可以直接访问你的网站的。

### CNAME绑定方式常见的问题

- 根域名(domain.com)可以访问，但是其它子域名(如www.domain.com)无法访问，可能因为你没有为子域名做解析，或者没有做泛解析。
- 子域名可以访问，但是根域名无法访问，可能是没有为根域名做解析。


## 域名跳转

FarBox不提供从A域名到B域名的直接跳转，同时，这也不是我们建议的操作。

如果确实因为特定的原因有这个需求，你可以通过修改模板来实现这个效果。

比如有一个场景，某用户原来使用`hello.farbox.com`，后来申请了`hello.com`。


```
{# 这段代码应该是可以被所有页面调用的，比如base.html #}
{% if request.host == 'hello.farbox.com' %} 
	{{ redirect('http://hello.com' + request.url_without_host ) }}
{%endif%}
```


