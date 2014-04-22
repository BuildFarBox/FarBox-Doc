Title: FAQ
url: faq

## About FarBox

### How FarBox works?
When you save files into Dropbox, or use FarBox Editor and other Apps, we do something like these:

1. Fetch the updated file content, and make it structured;
2. When your visitors come, the template API will render the page dynamicly.

In short, Dropbox is your hard disk on cloud, FarBox makes it live; a disk becomes a Web Server. 

### Can FarBox access my other data on Drobox？

No! Except the APP folder of FarBox, we have no permission to access other data.

### Would Dropbox limit the traffic of my sites？
No. Your sites' traffic cames from FarBox, not Dropbox.

### Is FarBox a static website hoster?
No. We are absolutely dynamic site creator! 

We supports comments, statistics, visits of a post, and you can custom everything dynamically by the Template API.

### Can I host a static site?
Yes, you can use FarBox in this way.

Make sure there is a `index.html` in  your site folder or a subfolder (make sure the URL is endswith `/`).


## Site Related


### What does `Site Folder` mean？
> Site Folder is a direct sub folder of FarBox, it means a website.
```
    --| FarBox (created by Dropbox)
        --| site-folder-1 （this is a Site Folder）
            --| sub folder (not a Site Folder)
		--| site-folder-2 (another Site Folder)
```

### How to create/delete a site?

Just create/delete a Site Folder. It is a common and recommended way to use a domain (like test.farbox.com) to name your site folder.



### Why is my site domain that  ends with .r.farbox.com so long?

This is your temporary domain, maybe you did not set a domain to your site yet, or the domain you set is conflicted to others.

### What does `Categories` means?

It usually means the categories of your posts, and the category name is the name of the parent folder of a post.

But you can custom everything in FarBox, so, some template designers, do not treate a floder as a category.

### If using the FarBox comments system, where the data stored?
If a visitor committed a new comment to a post or an image, the related comments file (plaintext) will be generated at the same place of the post (or image).

Of cousre, it will be synced to your Dropbox, and your computer too.


## Sync Related


### My images can't be synced, and if I modify the title of a post, the post will be disappeared.

Please check `http://yourdomain.com/service/status`, if  the status of token is not `valid`, you need to re-grant the token.

How to re-grant? Go to the homepage of `farbox.com`, use your Dropbox account to login again.

### Some of my posts, not existed in my Dropbox, but existed on the Website, why?

This is a small probability event. Because sometimes, Dropbox did not capture the event of a file `moved` or `renamed`, but FarBox Editor (or other APPs) did...

In general, your website will check the conflict files every 2 hours, then these files will be deleted.


### Some of my posts disappeared, why?

If you are using FarBox Editor, but Dropbox was shutdown more than 2 hours. Some of the newest posts will disappear.

How to fix it? Restart your Dropbox, after syncing, everything will be ok again.


