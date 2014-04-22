Title: Realtime API
URL： realtime-api
Date: 2014-04-15 17:29:27

## Summary For Realtime API

Realtime API needs the Websocket's connection that Chrome、FireFox、Safari、IE>10 .etc support.


Some notices about FarBox Realtime API:
1. No data transferred through a connection in 60 minutes, the connection will be closed;
2. A connection lasts no more than 24 hours.
3. Avoid to build connections too often; if the server think you are attacking the API, it will block your IP.


## File Changed Notification API

> When a file in your site folder was changed, the Websocket of visitors' browser would receive a notification.
> FarBox just pushed the event that inclued some information about a file, if more details needed,  you should use other Template API to get them.

API URL: `ws://realtime.farbox.com/notes`l If current site is under `HTTPS`, using `wws:` instead of `ws:`.

Notices:
- One file change is related to only one notification.
- Must use the API in your current site, otherwise, no data transferred through the Websocket.

### Code snippet
The sinippet is in `CoffeeScipt`, and `message.data` is the strings in JSON format.
```coffeescript
if WebSocket? and JSON?
    if document.location.protocol == 'https:' then ws_protocl='wss:' else ws_protocl='ws:'
    ws_url = ws_protocl+'realtime.farbox.com/notes'
    socket = new WebSocket(ws_url)
    socket.onmessage = (message)->
        note = JSON.parse(message.data)
```

### Fields of Noticafication

| Field | Description |
| --- | --- |
| site_id | -- |
| path | changed file's path (relative to site folder) |
| doc_type| like post/image/template|
| is_deleted | is the file deleted or not |
| date | event date, integer type |



## AutoReload Mode

`AutoReload` mode means when template files (html/jade/css/scss/js/les) are changed, the webpage will be re-rendered or autoreloaded.

It is a great friend-feature to developers, it goes without  other tools.

And you can modify the template file to replace `/template/js/autoreload.js` to custom your own AutoReload feature.

The config of `autoreload`, refers [here](//doc.farbox.com/read/site-configs#toc_22).






