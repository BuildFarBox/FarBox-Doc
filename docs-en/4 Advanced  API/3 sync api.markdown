title: Sync API
url: sync-gateway
toc: yes

## Basic Sync API

### API URL

The API URL is `/service/gateway/sync`, and the only acceptable method is `POST`.

### Fields of POST

- fb_token: a token to control the data of a account; if current use is login, this parameter is not required, but usually you need to call [function `need_admin`](functions#toc_14) at the first.
- push_to_cloud: if the `fb_token` parameter is used, and this value is `true` （ default value is `false`）, then the file will sync to Dropbox immediately. If `fb_token` is not used, push_to_cloud will always to be `true`.
- path: the file path. If user is login and is owner, the path is relative to current site; otherwise, relative to the root direcotry `FarBox`.
- is_deleted: if true, means to delete this file. If only one field in POST, and the field is `path`, it also means `is_deleted=true`.
- is_dir: if true, means current path is a direcotry.
- raw_content: the raw content of the file.
- base64: equal to `raw_content`, but accept the content in base64 format.
- file: euqal to `raw_content` when using `multipart/form-data` to upload a file.


### Code Snippet

**Make sure current login user is the owner of the site**

```js
$.post(
    '/service/gateway/sync',
    {
        path: 'this-is-a-test.txt',
        raw_content: 'post content'
    }
```

After calling this code, a new file (`this-is-a-test.txt`) whose content is `post content` will be added in a site folder of yours.


### Notices

- except field `path`，other fields are provided on demand.
- if `push_to_cloud` is not set, the file won't sync to Dropbox, and its live time in FarBox is 2 hours.
- the priority of geting a file raw content from these fields: file > raw_content > base64 .
- if your interaction is very complicated, maybe guid your user go to Dropbox (or his/her computer ) to operate is a better way.
- only when current login user is owner, the action of deleting a file will sync to Dropbox.

## Public Sync API

> This API is working on `Basic Sync API`, but it allows other accounts even a anonymous account to push data to your site and your Dropbox.
> Make sure the site config of `sync_by_3rd` is `yes`; if you want anonymous accounts to push data, the site config of `sync_by_3rd_public` also should be `yes`.

### API URL

The API URL is `/service/gateway/sync_3rd`, and the only acceptable method is `POST`.


### Allot Space Automatically

Every time accepted a file incoming, the system will compute a filepath to store it automatically, and the path is always under the directory named `share`.

You can also declare a subfolder name under `share`, if not, the default value is `default`. Then the filepath will be like `<subfolder>/year/year-month/year-month-day hour-minutes-seconds`.

If current user has the permission to edit the file, please declare the `path` field in POST.

How can other acount edit a file? If  `metadata.account_id` of a document is equal to the account_id of current login account, editing is allowed.


### Fields of POST
- fb_token: not required
- push_to_cloud: not required, always be `true`
- path: not required, declare it on demand
- is_deleted: not allowed
- is_dir: not allowed
- folder_3rd:  specify a subfolder, default value is `default`
- ext_3rd: suffix of a file, default value is `txt`
- raw_content、base64、file: same usages to `Basic Sync API`



## Limits

### Basic Sync API
- less than 2,000 times per 10 minutes per account
- less than 5,000 times per hour per account
- less than 25,000 times per day per account

### Public Sync API
- less than 5 times per 10 minutes per account
- less than 2 times per 10 minutes per IP for anonymous account
- less than 300 times per 10 minutes per site



