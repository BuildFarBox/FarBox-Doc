Title: Site Domains
url: domain

## Domains Related

### Subdomains of FarBox.com

We offer free subdomains of FarBox, like `***.farbox.com`, the max-length is 3.

To occupy a subdomain without a reasonable reason is not allowed. For example, if you occupy a hundred subdomains, we would suspend your account.

*btw, do not try to occupy the subdomains that related to the brand of FarBox, like blog.farbox.com, wiki.farbox.com, app.farbox.com .etc*

### How does the domain work?

When a visitor visits `example.com`:
step1. `example.com` will be resolved, and points to the server of FarBox;
step2. the server of FarBox will find the site matched to the domain `example.com`.

If the domain is a subdomain like `***.farbox.com`, the step1 won't be triggered; otherwise, if using independent domain, you need to modify the DNS Records to point to the server of FarBox.

btw, the step2 is handled by the Server automaticly.

## Bind Independent Domain

### Option 1: Modify NameServer
If you know how to manager your DNS, please modify your domain NameServers (with your domain registrar not the DNS hoster) and set them to `ns1.farbox.net` `ns2.farbox.net`.

Before doing this, you should bind the domain to your site at the site admin dashboard first.

If you do not know how DNS works, just go to the site admin dashboard, buy a domain. And we will handle the stuffs automaticly. (*available soon*)


### Option 2: Modify CNAME of DNS

If you want to bind `yourdomain.com`, you need to create a CNAME record of this domain to point to `yourdomain.com.park.farbox.net`.

In `yourdomain.com.park.farbox.net`, `yourdomain.com` helps FarBox to find your site, `park.farbox.net` helps the DNS to find the Server of FarBox.

### Option 3: Modify A Record (not recommended)

> Avoid to use this solution if possible, when FarBox changes the IP, A record will fail to work.
> And the Network Optimization of FarBox does not work for A Record.

If your root domain can't use CNAME, you could use A Record instead. The IP of A Record is related to `yourdomain.com.park.farbox.net`.


Usually, these two terminal commands of OS can help you to find the IP for A Record.
```
ping yourdomain.com.park.farbox.net
nslookup yourdomain.com.park.farbox.net
```


### How to Bind Subdomains?

Assume you want to bind `blog.yourdomain.com`, if the binding is through `Modify NameServer`, without doing anything, just use it!

If through `CNAME`, you need to set a new CNAME Record to point to `blog.yourdomain.com.park.farbox.net`.


## FAQ

### I changed the DNS records, but it does not work?

After changing DNS records, you have to wait senconds or minutes, even more than hours, it depends on your domain registrar or DNS hoster.

btw, `yourdomain.com.park.farbox.net` is used to bind your domain, but you also can visit it directly.

### Root Domain and Subdomains Problems by CNAME Binding.

- Root domain (like domain.com) is not accessible, but other subdomains (like www.domain.com) is. Maybe you did not set DNS records for your root domain.
- Subdomains is not accessible, but root domain is. Mabye you need to set records for your subdomains, or set a wildcard record for your root domain.


## Domain Redirect

FarBox does not support `jump from A domain to B domain`, and this action is not recommended.

If you indeed want this feature, you can modify your template file to handle it.


For example, you used `hello.farbox.com`, and now you have `hello.com`.

```
{# make sure the code below, used by every page; maybe `base.html` is a good place. #}
{% if request.host == 'hello.farbox.com' %} 
	{{ redirect('http://hello.com' + request.url_without_host ) }}
{%endif%}
```


