title: 同步接口
url: sync-gateway
toc: yes

## 基本同步接口

### 接口url与传输方式

接口的url为`/service/gateway/sync`

传输方式仅仅支持POST。

### 传递数据的字段

- fb_token 可以操作账户数据的token，通过登录接口获取；如果当前用户在当前网站已经登录，则不需要这个参数，但通常需要在页面中调用[`need_admin`这个函数](functions#toc_14)。
- push_to_cloud 在有client-token参数下生效，如果为`true`，则表示会重新推送回Dropbox; 默认为`false`。如果不是通过fb_token的方式，那么push_to_cloud始终为`true`。
- path 文件的路径，如果当前用户是当前网站的站长，则路径是相对站点目录而言的；反之，则是相对`FarBox`这个根目录而言的。
- is_deleted: 如果为true，则表示是对当前文件进行删除操作；如果仅有path一个字段，则默认为true。
- is_dir: 如果为true，表示当前路径对应的是一个文件夹。
- raw_content 文件的原始内容，通过这个字段，可以不使用form来实现上传。
- base64 同raw_content, 但接受base64的格式
- file 使用`multipart/form-data`进行上传时，文件的字段名。


### 代码简要示例 

**条件：当前登录用户是当前网站的主人**

```js
$.post(
    '/service/gateway/sync',
    {
        path: 'this-is-a-test.txt',
        raw_content: 'post content'
    }
```

这样一来，当前用户的站点目录内会多出一个文件`this-is-a-test.txt`，内容为`post content`。


### 注意事项

- 只有path字段是必须的，其它字段都是按需提供。
- 如果`push_to_cloud`没有设置的话，则数据不会同步回Dropbox，在FarBox上的存活时间则不超过2个小时。
- 取文件源内容的优先级为： file > raw_content > base64 。
- 如果是极其复杂的交互，我们建议引导用户到Dropbox（或者个人电脑中）自行操作。
- 当前网站，所有者正处于登录的状态下，其删除行为，会同步回自己的Dropbox存储中。

## 公开式同步接口
> 这是`基本同步接口`的衍生，允许其他用户，甚至是匿名用户，向你的网站与Dropbox内推送数据。
> 需要确保站点设置中的`sync_by_3rd`已经开启，如果允许匿名用户，并且同时`sync_by_3rd_public`也需要开启。

### 接口url与传输方式

接口的url为`/service/gateway/sync_3rd`

传输方式仅仅支持POST。

### 空间自动分派
每一次数据上传，系统会自动指定一个路径来进行存放。并且，始终以`share`这个目录作为根目录。

可以申明一个`share`下的子目录，如果不声明，则会使用`default`。之后，则按照`年/年-月/年-月-日 时-分-秒`这样个格式组装路径。

如果当前登录的用户对某个路径拥有编辑的权限，则可以在POST字段中，增加`path`字段。

如何判断编辑文档的权限？某个路径的文档数据对象中，如果`metadata.account_id`跟登录用户的id一致，则可进行编辑。一般能用在文章类型的文件中。


### 传递数据的字段
- fb_token: 不需要
- push_to_cloud: 不需要，始终为True
- path: 在没有编辑权限之时，不需要，由系统自动分派
- is_deleted: 不允许
- is_dir: 不允许
- folder_3rd: 指定一个目录, 默认为`default`
- ext_3rd: 文件的后缀名，默认为`txt`
- raw_content、base64、file的使用同`基本同步接口`



## 限制

### 基本同步接口
- 同步接口的调用，每个账户，每10分钟内不能超过2, 000次。
- 同步接口的调用，每个账户，1小时内不能超过5, 000次。
- 同步接口的调用，每个账户，1天内不能超过25, 000次。

### 公开同步接口
- 登录了的账户，每10分钟内不能超过5次
- 匿名账户，每IP每10分钟内不能超过2次
- 对应某个站点，每10分钟内不能超过300次调用



