---
title: Domains and DNS
description: Detailed information on adding a domain to your Pantheon Drupal or WordPress site.
category:
  - developing
  - managing
  - going-live
---
Configuring your domain's DNS is required to route traffic to your Pantheon site.

## Step 1: Determine the URL to Serve From

We recommend using the [`HTTPS` protocol](https://en.wikipedia.org/wiki/HTTPS) and the `www` subdomain prefix for all sites. See  [http://www.yes-www.org/why-use-www/](http://www.yes-www.org/why-use-www/) for information on why `www` is recommended with modern platform providers. If your site will use `HTTPS`, either [enable SSL](/docs/articles/sites/domains/adding-a-ssl-certificate-for-secure-https-communication/) or use [Cloudflare's free SSL](/docs/guides/ssl-with-cloudflare/) before continuing.

## Step 2: Add Domains to the Site Environment
<div class="alert alert-danger" role="alert">
<h4>Important</h4>If you are using HTTPS protocol, <a href="/docs/articles/sites/domains/adding-a-ssl-certificate-for-secure-https-communication/">Enable SSL</a> before adding the domain to the site environment. The recommended DNS settings are different for HTTPS sites.</div>

You must have a paying plan to add a domain to a site environment. For more information, see [Selecting a Plan](/docs/articles/sites/settings/selecting-a-plan/).

1. From your Site Dashboard, select the environment to serve from the domain (typically Live), and click **Domains/SSL**.
2. On the Domain Setup tab, enter the domain name you want associated with that environment, and click **Add New Domain to the Live Environment**.

You can simultaneously add both the bare domain name and the `www` subdomain. This is highly recommended, as you will not be able to redirect traffic from one to the other without adding both.

<div class="alert alert-info" role="alert">
<h4>Note</h4>Add all domains you wish to resolve to Pantheon within the desired environments. Automatic resolution of domains and wildcards are not supported.</div>

### Develop Using a Domain Without Changing DNS
Sometimes it's useful to develop on a site using a specific domain, but the overhead of temporarily changing DNS is too much. Use the following workaround to allow your local workstation to access your Pantheon site by the desired domain without changing DNS. (Requires a paid plan)

1. From the Pantheon Dashboard, add the domain to the target site environment.
2. Add a line to your local <a href="https://en.wikipedia.org/wiki/Hosts_(file)">Hosts file</a> with  the provided IP address and the domain.

Example:
```
192.237.142.203 example.com
```

Remove this entry when you're ready to configure DNS.
## Step 3: Configure Your DNS
From the Live environment's Domains/SSL tool, click  **Show recommended DNS records** to the right of the domains you've added.

<div class="alert alert-danger" role="alert">
<h4>Important</h4><strong>Pantheon does not register domains or manage DNS.</strong> You will need to make these changes yourself at the registrar and/or DNS host for the domain; we cannot do it for you.</div>

Using the provided destinations in the site Dashboard, create the recommended DNS entries at the domain's DNS provider. Pantheon's www-redirection service will automatically redirect requests to the `www` subdomain.

### Serving Sites from Bare Domains with HTTP
To serve your site from the bare domain, you must:

1. Select a DNS provider that supports CNAME flattening, such as [CloudFlare (recommended)](https://support.cloudflare.com/hc/en-us/articles/200169056-CNAME-Flattening-RFC-compliant-support-for-CNAME-at-the-root), [ClouDNS](https://www.cloudns.net/features/), or [NameCheap](https://www.namecheap.com/domains/freedns.aspx).
2. Do not add the recommended DNS entries from the Dashboard. Instead, create CNAME records for **both** the bare domain (@) and the `www` subdomain, pointing to `live-yoursite.pantheon.io`.
3. [Redirect incoming requests](/docs/articles/sites/code/redirect-incoming-requests/#redirect-to-a-common-domain) to the bare domain via `settings.php` or `wp-config.php` to prevent problematic session handling and improve SEO.

One alternative to CNAME flattening is to use **[ALIAS/ANAME records](http://help.dnsmadeeasy.com/spry_menu/aname-records/)**. These records constantly monitor all resolving IPs of the destination (e.g. `live-yoursite.pantheon.io`), and creates corresponding A records.

Learn more about ANAME records:

*   [DNSimple](http://support.dnsimple.com/articles/differences-between-a-cname-alias-url/)
*   [DNS Made Easy](http://www.dnsmadeeasy.com/services/aname-records/)
*   [EasyDNS](http://docs.easydns.com/aname-records/)



## Frequently Asked Questions

### How long do DNS changes typically take?
DNS changes can take up to 48 hours to propagate across the entire Internet, but most updates happen much faster depending on the set TTL (Time to Live).

### How do I use Pantheon's WWW redirection service?
The www-redirection service listens for requests and issues 301 redirects with `www` prepended to the host header. To use this service, simply configure the domain's DNS with the recommended DNS records within the site Dashboard.

![](/source/docs/assets/images/desk_images/376194.png)

### My domain has an extra "www."
If you find that `www.example.com` resolves to `www.www.example.com`, or `subdomain.example.com` resolves to `www.subdomain.example.com` - the domain's `www` entry has been improperly configured as an A record.
![](/source/docs/assets/images/desk_images/376201.png)
Correct this problem by setting the `www` entry as a CNAME record pointing to the recommended destination (e.g. `live-yoursite.pantheon.io`), found within the site Dashboard on the target environment.

### Can a site on Pantheon be used with a third-party reverse proxy?

Yes, many Pantheon customers use third-party reverse proxies, such as [CloudFlare](https://www.cloudflare.com/). If you'd like to do this, do not direct traffic to a *.pantheon.io domain. Instead, associate an intermediate domain with the live environment and create the appropriate DNS entries, then point your reverse proxy to the intermediate domain.

### Can I test my domain name without making DNS changes?

Yes, see [above](/docs/articles/sites/domains/#develop-using-a-domain-without-changing-dns) for details.

### Why isn't my site loaded when I ping the provided Pantheon IP?
The provided IP address resolves to our load-balancers. When a request comes in, it is automatically routed to the proper site.

### A contrib module that I use for my Drupal site does not support IPv6; how should I proceed?
[Use the issue queue](https://drupal.org/node/317) of the module in question to communicate with the module maintainers.

### My site is completely incompatible with IPv6 traffic; how can I force IPv4 traffic?
Do not create an AAAA (IPv6) record as recommended in the site Dashboard when configuring the domain's DNS. Only create the CNAME record for the `www` subdomain and the A record for the bare domain.
##See Also

* [Redirect to a Common Domain](/docs/articles/sites/code/redirect-incoming-requests/#redirect-to-a-common-domain)
* [Redirecting to HTTPS](/docs/articles/sites/code/redirect-incoming-requests/#redirecting-to-https)
* [Enable SSL for Secure HTTPS Communication](/docs/articles/sites/domains/adding-a-ssl-certificate-for-secure-https-communication/)
