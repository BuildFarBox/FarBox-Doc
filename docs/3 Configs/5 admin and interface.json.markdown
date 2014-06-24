Title: Admin Dashboard Configs
URL: admin-configs
Date: 2014-02-02 13:36:11
toc: yes

## Admin Dashboard

We can set configs to a site by modifying [site.txt](site-configs).

The defualt template of FarBox renders the admin dashboard （URL is `/admin`） by the template file `admin/index.html`,

The implementation logic:

1. Get the data about curent site configs and other information by template file `admin/data.html`.
2. Build the dashboard interface by the rules defined in `interface.json`.
3. When click the `save` button, the config properties will be turned into the plaintext content of `site.txt`, and sync to FarBox and Dropbox.

> [Source Code refers here](https://github.com/BuildFarBox/FarBox-Admin/tree/master/admin)，index.html handles HTML page, and data.html handels the JSON data to be loaded, and some css/js files in  `dash` directory.

## interface.json

> default interface.json[source code](https://github.com/BuildFarBox/default-template/blob/master/template/interface.json)

### Basic Structure

![Image Title](/_image/configs/interface-en.png)

#### level-1: pages， type is []

`pages` will build some clickable tabs.

If only one item in pages, and a property of this item is `extends: true`, then other pages will inherite from the default interface.json.


#### level-2: page
- title: title of the tab
- groups: groups of configs, similar to the HTML tag `fieldset`

#### level-3: group
- title: title of the group
- cells: a container cells

#### level-4: cell
- parts_per_line: how many parts per line in a cell
- parts： container for detail configs

#### level-5: part
- title: name of the config
- key： the key of a config, such as `title`, which can be called by {{ site.configs.title }}
- model: one of them: text/select/check/textarea/image
- default_value: the default value. if model is text or textarea, the value will be displayed in `placeholder`; if the model is select or check, the matched option will be selected or checked.
- is_list： available for textarea model, means when re-format in YAML, this will be a list.
- options: available for mode select
- value: available for model image, it's a filename, like `/_image/avatar.jpg`, the user can drag an image and store it in this specified path.

### About 'Options'
- title: a display value
- vlaue: the real value
**Usually, options is a list, but if the value of a `options` is `root` (a strings), it will show the folder structure of current site.**

### Multi-languages Supports

The fields `title` and `potions` support multi-languages.
For example:
```
{
    "title": "default title",
    "title_zh_cn"： "if browser language is zh-cn, it will work."
}
```
Notice，`title_zh_cn` must be in lower-case.

## Notices
If your template need a speical field, like `site.configs.sub_title`, then config it through `interface.json` would be great!

If the rules make you feel confused, maybe just modify the default `interface.json` would be a easier way to custom a special admin dashboard.





