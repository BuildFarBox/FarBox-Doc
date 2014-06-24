Date: 2014-05-11 00:54:21
Title: Site URLs

All the URLs of a site are decided by the paths of template files.

For example, the template file has a path `template/post.html`, and it will handle the URL `http://domain.com/post`.

## Rules of URL Matching

> The filename of template files in examples below ends with `.html`, in fact, `.jade` is equal to `.html`.
> and `/template/folder/index.html` is equal to `/template/folder.html`.

If a URL ends with `.txt` `.mk`  `.md` `.makrdown`, the Template Engine will try to find a post with the same path, and will ask `template/markdown.html` to render it; if the template file not exists, then continue.

1. step1, try to match index.html for homepage.
- step2, try to match the full URL, for example `/path/sub_path.html` will match `/path/sub_path`.
- step3, try to match the first path of URL, `first_path.html` will match `/first_path/sub_path`, if there is no template file `/first_path/sub_path.html`.
- step4, try to use `I-Love-FarBox.html` to match all the URLs.(*be careful to use this rule*)
- step5, finnaly, will use `404.html` as `Not Found` page.

**Tip: `first path of URL` means the characters before first `~` or slash**
```
/path/sub_path the first path is `path`
/path/sub_path/~hello/world the first path is `path/sub_path`
```

## Mixed URL

If `+` in the name of a template file, it can match multi-URLs.

For example `template/index+tags+archive.html` can match `HomePage(/)`、`/tags` and `/archive`.


## Notices
1. The template file path should not include `.`, `#` and non-Enginlish characters.
- `/t/*` is a system URL，equal to `/template/*`; and all the resources under the folder of `/template/` should use this path: `/template/<path>`.


## Native Support for Scss/Sass/Less/CoffeeScript

For example, only when visiting `http://yoursite.com/template/style.scss?format=raw`, the file is in raw `Scss` format, otherwise, it is compiled into CSS format automatically.

One other thing to note is the Scss compiler will ignore logic like include/import which needs multi-files, and the `Less` compiler is same to Scss compiler, so the `extend` is not supported.

And FarBox supports to compile a `.coffee` script into a `.js file` automatically.

