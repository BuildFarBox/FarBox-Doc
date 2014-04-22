Title: Mechanism and Limits
url: mechanism-and-limits
Date: 2014-02-10 12:59:33


## Files Supports and Limits

### File Types FarBox Supports

| Type | Suffix | 
| --- | ---- |
| image | bmp gif jpeg png pjpeg tiff |
| frontend resources | css html htm js json ico xml swf less sass scss |
| compressed file  | zip tar gz rar 7z |
| document | pdf txt config cvs |
| fonts | woff ttf otf eot |

### Limits
| Type | Description |
| --- | ---- |
| hidden files  | ignore, `/.` in a path or path endswith`~`|
| max file size | 15Mb, if exceed then ignore |
| post (article) | less than 3Mb, if exceed then ignore. and only pick the first 500,000 bytes |
| HTML related files | less than 3Mb, if exceed then ignore |
| template resources | less than 500Kb，if exceed then ignore |
| folders in template| ignore |
| tempalte files count | less than 200, if exceed then ignore |

*HTML related files include a file with these suffixes: html htm css js json swf xml less sass scss*

## Sync Mechanism

### Sync with Dropbox
1. FarBox keep syncing from Dropbox in realtime.
2. FarBox also try to sync from Dropbox again every 10 minutes per account.

### Sync to FarBox

Using FarBox API to store a file, the file will sync to Dropbox too. And there will be a notification to be pushed to the connections from websocket.



