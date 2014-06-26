Title: Account and Token
url: account-and-token

## Login/Logout

The login URL is `/login`, will redirect to Dropbox to get granted.
The logout URL is `/logout`, will clear the login information and jump to the homepage.


`/login` accepts the parameters in GET method:

- utc_offset: if not set, will redirect once to get it from browser; if set to `ignore`, just ignore; if other values (float type), after login, utc_offset of your account will be updated.
- redirect: should be URL that to be redirected after login.
- return_token: if this parameter exists, will redirect to  `/service/blank?token=xxxxx` after login, and  the `xxxxx` is the fb_token that can control your data on FarBox.
- refresh_token:  the effect is close to `return_token`, but will renew the `fb_token`.
- auto_close: if the value is `true`, will return a page whose code is `<script>if(window.opener!=undefined){window.opener.returnValue=true}else{window.returnValue=true};window.close()</script>`

 

## Permissions of FarBox

### Permissions of FarBox on Dropbox

All our permissions are restricted to the APP folder named `FarBox`.


### Permissions of Other APPs for FarBox

Other APPs for FarBox can connect to FarBox server through the API, and all the files changed will be synced to your Dropbox account too.

If the APP is not hosted on FarBox, it can't login, all the operations will be verified by fb_token, and have no permission to delete a file.


## Account Security

Dropbox will store all the versions (about last 30 days or more) of a file, even includes the deleted one. So you can recover your data when something unexpected happened.

The main feature of FarBox is rendering your data on the internet. We do not handle the  high security data. Make sure your Dropbox account is safe, then the data on FarBox will be safe.
