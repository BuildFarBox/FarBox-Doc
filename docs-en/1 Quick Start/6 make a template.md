title: Custom Template
url: how-to-make-a-template

## Step One, Find the Template Folder

**Assume your current site folder is `Dropbox/APPs/FarBox/MyBlog/`.**

The template files are under  `Dropbox/APPs/FarBox/MyBlog/template/`, if the folder not exists, create it first.

And `Dropbox/APPs/FarBox/MyBlog/template/index.jade` will be your homepage.



## Step Two, Download a Template Package

Maybe you should try our [default template](https://github.com/BuildFarBox/default-template) first.

And there are some [repos](https://github.com/BuildFarBox/) we host on Github. Of couse, you can also [Search](https://github.com/search?o=desc&q=farbox&ref=cmdform&s=updated&type=Repositories) other people's template packages.


After downloaded a template package, you need to put the template files into your `template` folder.


Here are some errors of location:
```
Dropbox/APPs/FarBox/MyBlog/template/index.jade --> correct

Dropbox/APPs/FarBox/MyBlog/template/Name/index.jade --> error
Dropbox/APPs/FarBox/MyBlog/template/Name/template/index.jade --> error
```


## Step Three, Know Some FarBox Template Syntax

```jade
head
	if site.title
		title= site.title
body
	for post in posts
		h1= post.title
```

Is this Template API Syntax beautiful?  Details refer here [Template Engine](template-engine).


## AutoReload and Realtime, Start to Custom!

After synced your template files, you can also modify them on our onlie code editor, the URL is `http://yourdomain.com/admin/code`.

When you modify a template file, your site will be autoreloaded, between different browsers, different devices(computer, mobile, PAD). No need to refresh by hand, and the pages are rendered in realtime.


