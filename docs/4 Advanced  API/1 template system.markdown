title: Template System
url: farbox-template-system
toc: yes

## Template Package

A template_key is the unique identifier for a template package.

A template package can decide the interface of a website, even includes the admin dashboard, text editor and code editor.

More details refers [Template Package Configs](template-configs).

### Clone Template
The template of every site on FarBox can be cloned (except forbided by the designer).

Clone a template dose not mean you can get the original template files, of course, many designers' templates are free to download, you can get them from other places like GitHub.

Usually, if you want to clone `http://www.domain.com`, just visit  `http://www.domain.com/template`.

### Clone in Two Ways
> Note: If you set `template priority` in your site configs, although `clone` works but may be not effective.

#### Auto Update (package mode)

After cloned, the information set to your site is a `template_key`.

When the designer of the original site update the package,  all the users of this package will get the newest template resources automatically.

#### Just Clone (clone mode)

Different to `package mode`, if you cloned from A site, even A site changed its template files, nothing will happen to you site.

## Template API

### Template Preview

Assume a template has a template_key=`xxxxxxxxxxxxx`, and your site domain is `www.domain.com`; then visiting the url `http://www.domain.com/?template_key=xxxxxxxxxxx` to preview the template on your site.

**Notice: the visitor who can preview a template must be the owner of a site.**

### Clone Template

#### Option 1: URL

Assume you want to clone `site-to-clone.com`(if you knew the site_id, use site_id instead of domain), and your own site domain is `yourdomain.com`.

Visiting `http://yourdomain.com/?template_site_id=site-to-clone.com` to complete clone.

The default option is in `clone mode`, if you need `package mode`, append `&auto_update=true` to the URL metioned above. 


#### Option 2: AJAX

`/service/gateway/clone_template_by_key`, the role of this URL is to handle the template clone, accept `POST` method.

Accept three parameters:
- site_id： your site id
- template_key：the template_key of the site you want to clone, default value is `default` which means default template
- auto_update: true or false, means `package mode` or not

If clone succeed, it will return `ok`; if you are not login, the status code of returned response  is 401.

And if you just want to clear the old template package information on your site, have a try to set `template_key` to `default`.




