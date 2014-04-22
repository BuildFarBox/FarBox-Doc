Title: 账户与授权
url: account-and-token

## 登录、登出
用户登录的URL为`/login`，会自动跳转到Dropbox的授权网址进行授权（也就是登录）。
用户登出的URL为`/logout`,  清除登录状态，并跳转到网站首页。

`/login`接受的GET参数:

- utc_offset: 如果为空，则会进行一次跳转，获取当前浏览器的时差；如果为`ignore`，则表示不处理；如果指定值(类型为正负浮点数，比如北京时间则设置`8.0`)，则在登录成功后，调整当前账户的时差。
- redirect: 为一个网址，如果有指定，则会在登录成功后，跳转到这个网址。
- return_token: 不管是什么值，只要有这个参数，最终会跳转到`/service/blank?token=xxxxx`的地址，其中`xxxxx`就是可以直接对你的FarBox数据进行操作的token。
- refresh_token:  效果同return_token, 但是会更新当前账户的原token。
- auto_close: 如果为`true`,  则会返回`<script>if(window.opener!=undefined){window.opener.returnValue=true}else{window.returnValue=true};window.close()</script>`

 

## FarBox的权限

### FarBox在Dropbox的权限

我们对你的Dropbox账户的数据操作权限，仅限于`FarBox`这个APP目录。

### FarBox对其它应用的权限

你也可以通过其它应用可以直接连接自己的FarBox API，从而获得对数据的操作权限，所有文件的变动，最终也会重新同步回你的Dropbox账户内。

如果其它应用不是托管在FarBox上的，它无法实现FarBox上的登录，只能通过token进行操作，则不具备对文档的删除权限。

如果其它应用是托管在FarBox上的，并且你在当前应用的网站上处于登录的状态，则它将具备对文档的删除权限。


## 账户安全

Dropbox会对所有数据的历史记录，包括已经删除的、文件的早期版本都会30天(或以上)的备份，不管发生什么，你都可以轻松恢复。

而FarBox的主要功能是将你的数据，更便捷地在互联网中呈现; 你的数据在FarBox中，相对而言是低密度的。所以，我们不处理账户的密码逻辑，而你只需要保证自己的Dropbox账户安全，就可以保证FarBox数据的安全。

