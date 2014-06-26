title: Other APIs
url: other-gateways


## Site and Account Status

URL： `<yourdomain.com>/service/status`

### Account Status

- Cloud Service: Third party cloud service. Except Dropbox, other clouds are not supported yet
- Token Status: if not `Normal`, please re-grant
- Last Synced at: last time of connecting to the cloud service
- Synced Times: times of connecting to the cloud service
- Last Changed at: last time a file is changed
- TimeZone: the timezone of current account


### Site Status

- Site ID: site id is `account_id + site_path`
- Last Updated: last updated date of this site
- Template Priority: template priority of this site


## Sync Logs
 
URL： `<yourdomain.com>/service/sync_logs`

This will show all the sync logs of your account.

Every file has no more than 500 logs.


## Avatar

URL：`/service/static_3rd/avatar/<email_md5>`

This is a proxy for `http://www.gravatar.com/`.

## HTTP Response Headers

> The response from FarBox has some special headers.

| Name | Description  |
| ----- | ---- | 
| x-cached | yes/no, `yes` means the page if from the cache of server |
| x-cache-key | a unique identifier |
| x-render-time | rendering time (seconds) of this page |
| x-template-path | the path of current template file |
| x-nano-hit | accelerated by Nano Network or not |
| x-nano-time | similar to x-render-time, time cost in Nano Network |


## Static Files

URL: `/service/static_3rd/staticfile/<file_path>`

This is a proxy for `http://cdnjs.cloudflare.com`.

`/service/static_3rd/staticfile/ajax/libs/font-awesome/4.0.3/css/font-awesome.min.css` is equal to  `http://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.0.3/css/font-awesome.min.css`



