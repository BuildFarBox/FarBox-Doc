title: Post Configs
url: post-configs

Use `{{ doc.metadata }}` to get the configs of a post.

**Special Note: if the filename of a post is `index.txt`,  the configs will be transferred to the document of its parent folder.**

For example, the content of `folder/index.txt` is:

```
title: folder_title
position: 1
date: 2013-12-11

description here
```

Then the document whose path is `folder/`, these properties `{{doc.title}}` `{{ doc.position}}` `{{doc.date}}` `{{ doc.content }}` `{{ doc.raw_content }}` on it will be updated.


### title

If `{{doc.metadata.title}}` exists,  `{{doc.title}}` will be equal to it.

If it is not declared, `{{ doc.title }}` will get from the first H1 tag of content or the filename of the post.


### date
If `{{doc.metadata.date}}` exists,  `{{doc.date}}` will be equal to it.

If `date` is not declared, system will try to get date by these rules:
1. filename like `2012-12-12 Title.txt` has a date `2012-12-12`;
2. the last modified date will be the date of the post

### position

If `{{doc.metadata.position}}` exists,  `{{doc.position}}` will be equal to it.

When calling `get_data`, position is a special value (`sort='position'`) to sort the records.

Besides in metadata, you can declare `position` by the filename. For example, `1.2 my article.txt` or `1.3 my other tiltes`, has position `1.2` or `1.3`. The blank space is a character to split position and normal title.

### status

Default value is `public`.

It can be any characters less than 20 letters.

### toc

If there are multi-headers（H1、H2、H3 .etc) in a post, the `toc` config can control show the table of contents or not in the main body of the post.

The `toc` config of a post has a higher priority to the `toc` config of the site.

Accept: yes/no


### url

A field to custom the url path for a post.

The value will be stored in `{{doc.url_path}}`.


### slot1

An extra field for fulltext search, this is a field to search.

### slot2

An extra field for fulltext search, this is a field for sorting.


