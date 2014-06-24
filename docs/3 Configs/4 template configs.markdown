title: Template Package Configs
url: template-configs


## Concept of Template Package 
`Template Package` is an archive of the template files under a site folder.

If the author update the template files, then the sites whose template package inherit from it will be updated too.

### readme.txt

`template/readme.txt` can help a template package to descript itself.

A simple demo of readme.txt：
```
key: farbox-template
title: My Template Name
domain: http://mysite.com

this is description
```
**`key` must be declared, other fields are decided by the designer of template. But you should know, the information of readme.txt is public to other people.**

When readme.txt is changed, package system will compute a template_key by `account_uid+key` as a unique identifier for the template package.



