Title: Config Syntax
url: syntax-of-configs


## The Config

`Config` means using the forepart of a text file as metadatas to extend the properties of the document.

Supports types: site, post, folder, template_package.

**We support .md .markdown .mk .text suffix for a text file, so if we talk about `site.txt`, it means a file with the same name but different suffix (like site.md) plays the same role.**


## Config Syntax

To define a `Config`, we should do it in the forepart of content.

Option 1： Every line using a format like `key: value`

```
title: I'am Title
url: this-is-url

content body
```

Then the configs is:

```json
{
	"title": "I'am Title",
	"url": "this-is-url"
}
```

- - - - - -


Option 2： Using YAML, start and end with a seperated line `---`.
```
---
key: value
title: Blogging in FarBox
---

content body
```

Then the configs is:

```json
{
	"key": "value",
	"title": "Blogging in FarBox"
}
```


### Notices
1. The key name can't start with a number
- Operational symbol is not allowed in a key name, like `+-x/`; for example, `post-x: value` is error, the correct syntax is `post_x: value`.
- Key name is case-insensitive, `KEY` is same to `key`.



