title: 模板系统
url: farbox-template-system
toc: yes

## 模板包

模板包Temlate Package会对应一个template_key, 这是网站模板的唯一标识。它除了定义一个网站的模板之外，还可以自定义网站的后台管理，文章编辑器等一切可能的自定义。

具体的，可以通过阅读[模板包的配置](template-configs)进一步了解。

### 模板克隆
每个FarBox托管的网站，其所使用的模板都可以被克隆，**但仅限于使用，源码不可修改**，当然，模板作者也可以放出源码提供下载。
一般情况下，假设想克隆`http://www.domain.com`, 那么只要到`http://www.domain.com/template`这个地址就能实现克隆。

### 克隆的两种方式
> 注意： 如果在你的网站设置中，对`模板优先级`等模板引擎相关属性有设置的，那么模板的克隆会成功，但未必会生效。

#### 同步更新(package模式)
这需要源网站将模板打包了（后面的Templat Package有详细说明），才能采用这种方式。
采用这种方式的，如果源网站的作者更新了Package，那么所有使用这个模板的人，都会实时同步过去。

#### 独立克隆(clone模式)
假设我现在克隆了A网站，A网站的模板后来又发生了变更，这并不会影响到我已克隆的模板。一般情况下是去掉`同步更新`的勾选，即是独立克隆的方式了。

但需要注意：克隆模板，并不会在你的文件夹内创建任何源码！


## 系统的模板接口

### 模板预览
假设需要预览的模板的template_key = `xxxxxxxxxxxxx`, 需要预览的网站域名为`www.domain.com`, 那么访问`http://www.domain.com/?template_key=xxxxxxxxxxx`即可实现模板的预览。

**预览有效的前提，是网站所有者，在自己的网站上处于登录的状态。**


### 克隆模板

#### 方式1:URL

假设需要克隆的网站域名为`site-to-clone.com`(如果你知道网站的{{site._id}}, 也可以用这个值替代)，你自己的网站域名为`yourdomain.com`。

那么访问`http://yourdomain.com/?template_site_id=site-to-clone.com`即可完成克隆。(如果你当前没有登录，则会自动跳转到登录页面)

默认的克隆方式是非自动更新的；如果需要自动更新的话，则在网址后面增加`&auto_update=true`即可。

#### 方式2:AJAX

`/service/gateway/clone_template_by_key`这个URL是处理模板克隆的，接受POST传值。

接受三个参数:
- site_id： 你自己的网站id
- template_key： 需要克隆的template_key, 默认为default，即默认模板。
- auto_update: 是否自动更新，如果为`true`，则是自动更新，即package模式。

如果克隆成功，会返回`ok`。

注意事项：
- 使用这个AJAX地址，需要确保处于登录状态，否则返回的response的HTTP状态码为401.
- 如果需要清除一个网站的模板克隆，只需要将template_key设为`default`就可以了.










