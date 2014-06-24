Title: Site Configs
Url: site-configs
toc: yes
---

Site configs is controlled by `site.txt` that is under your site folder. Usually we can modify configs through the URL `yoursite.domain.com/admin`, a interface of `site.txt`.

Use `{{ site.configs }}` to get the detail configs information. And `{{ site.configs }}`  is a loose set of k-v data. Except some special fields, all others are customed by yourself.

## Domain

### domain
The domain of current site. If it's a independent domain, you need to modify your DNS record, refers [Here](domain).


### domains
This is a list, a maximum of 10 items.

It looks like in `site.txt`:
```
---
domains:
  domain1.com
  domain2.com
---
site description
```

## Contents Display

### posts_per_page

Posts count per page, but it's not always effective。

In your template file, `get_data(type='post')` will use `site.configs.posts_per_page`; but `get_data(type='post', limit=10)` dose not.


### toc
If multi-headers（H1、H2、H3 .etc) in a post, the `post.toc` can get the `table of contents` from the headers.

In the site configs, if toc is set to `yes`, then `{{ post.content }}` will include `{{ post.toc }}`


Accept: yes/no，default value is no

### post_content_type

When calling `{{ post.content }}`, if the value is set to `plain`, return the plaintext with linebreaks; if other values, return the content compiled by Markdown syntax.

Accept:  plain/markdown, default value is markdown


### post_paragraph_indent
If the value is set to `yes`, `<style type="text/css">.post p{text-indent:2em}</style>` will be inserted into the HTML of a page to deal with indentation.

If the value is `hard`, means a indentation for each line. By the css rules, indentation is not available to `<br>`; so template system will rebuild the HTML struction of current page to replace `<br>`.


Accept: yes/no/hard，default value is no


### mathjax

If the value is set to `yes`, a mathjax script will be inserted into the page to deal with math formulas.

Accept: yes/no，default value is no


### file_path

If no path is declared when finding images or albums (using function `get_data`), this value will be the default value for `path`.


## Data and Security

### comment

Comments allowed or not.

Accept: yes/no，default value is yes

### sync_by_3rd

Other visitors can put a file into your `share` directory of site folder or not.

Accept: yes/no，default value is no

### sync_by_3rd_public

Allow a visitor who is not login to push file (`sync_by_3rd` must be `yes`) into your `share` directory.

Accept: yes/no，default value is no


### users

Add password for a user to visit your site, format is  `username@password`, one record each line.

Because the passwords are not stored in plaintext in FarBox, so some records will be displayed like `username@xxxxxxxxxxxxxxxxxxxxx`, they are effective too.



### score_degree

This is a sensitivity value of the dig system. The counting unit could be loosely described as `day`, `0.5` is a suitable value, it means the hostest & newest records will be digged in last (about) 24 hours.

The dig system is running for every site, if you want the digged recods, you shoud declare the sort='_score' when calling function `get_data`.

## Template Engine


### scripts_per_page

Google statistics code and some other comment system embeded code can use this field. It inserts the code you specified into the page before the position of `</body></html>`.

If there is a mark of `<!--scripts_per_page-->` in a template file, this will be the position for inserting.


### scripts_for_doc

The effect is close to `scripts_per_page`, but only effective after calling function `add_doc_actions` in the page (like a post detail page).

And the position mark is `<!--scripts_for_doc-->`.


### template_priority

If multi-template-packages and customed template are mixed in a site, the `template_priority` can help us to switch between them.

| Value | Description |
| ---- | ---- |
| auto | default value |
| self | priority is your customed template |
| default | special value, force to use default template |

- `self`， if no customed template files in your site, it is euqal to `auto`.
- `auto`, try to find a template package to inherit; if fail, try to use customed template files; if fail again, use default template.


### template_inherit

The default template is also the basical template for all the sites of FarBox. 

If the `template_inherit` is `no`, means there is no basical template for current site.


Accept: yes/no，default value is yes

### template_clone_allowed

Allow other people to clone the template of your site or not.  But if your site has no customed template files, this config is not available.

Accept: yes/no，default value is yes.

### autoreload

If `autoreload` is on, and the script `/template/js/autoreload.js` will be inserted to the page to deal with AutoReload.

Accept: yes/no/admin/auto, default value is auto。

`auto`: 10 minutes after template files are changed or current visitor is owner -> allowed.
`admin`： current visitor is owner -> allowed.
`no`: not allowed.
`yes`: always allowed.

## Other Features

### nano
Join Nnao Network will speed your website up for the visitors among different countries. Nano Network nodes' status: <http://status.farbox.org/service/nano-network>

**Note: when you are modifying template files, do not join Nano Network.**

Accept: yes/no，default value is no.


