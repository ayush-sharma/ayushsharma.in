---
layout: post
title:  "HTTP security headers: HTTP-Strict-Transport-Security"
number: 60
date:   2017-09-29 2:00
categories: security
---
The HSTS header is a security enhancement that prevents browsers from accessing web pages through the insecure HTTP protocol, thereby preventing protocol downgrade attacks. The way this works is that you add the HSTS header to your HTTPS responses, which the browser will save into its HSTS hosts cache. The next time the browser accesses that resource, it will remember not to access it using normal HTTP.

This header takes three values:
- `max-age=SECONDS`: The time, in seconds, that the browser should remember that this site is only to be accessed using HTTPS.
- `includeSubDomains`: If this optional parameter is specified, this rule applies to all of the site's subdomains as well.
- `preload`: This options indicates that you've preloaded your site on the HSTS preload list.

This header will additionally convert all HTTP requests for that domain to HTTPS, and refuse to load those resources in case there is something wrong with the certificate. Implementing this shouldn't be a problem, as long as you already redirect all insecure HTTP traffic to its HTTPS variant. If you don't have HTTPS set up yet, [you can do so for free with the LetsEncrypt certificate]({% post_url 2016-08-24-securing-nginx-with-lets-encrypt-free-ssl-certificate %}).

Adding this to Nginx is easy:

```nginx
add_header Strict-Transport-Security "max-age=604800; includeSubdomains;";
```

Since this header can only be set via an HTTPS response, the user will need to connect to your site using HTTPS at least once, to indicate that subsequent requests must be made using HTTPS only. Meaning that if they visit using normal HTTP, and you redirect to the HTTPS version on your server, for at least that one HTTP request, your users are vulnerable. You can get around this by submitting your website to an HSTS preload list which will add your domain to the browsers HSTS host list automatically. This means that even if users have never visited your website before, the browser will only load your website over HTTPS. The problem here is that in case there are issues with your HTTPS configuration and you want to switch to HTTP temporarily, you won't be able to, since browsers will now only connect to your site using HTTPS. In this case, you will have to wait for `max_seconds` for the header to expire. Preloading compounds this problem since you may have to remove your site from the preload list, which is not easy. My recommendation is to set a small `max_seconds` while you're testing, and avoid preloading until you're absolutely sure that your HTTPS will always work and there will be no need to visit using HTTP ever again.

You can also check your web browser's HSTS hosts list. For example, for Chrome, visit [chrome://net-internals/#hsts](chrome://net-internals/#hsts), and query any domain you're looking for.

## Resources
- [Wikipedia HSTS](https://en.wikipedia.org/wiki/HTTP_Strict_Transport_Security).
- [HSTS preload list](https://hstspreload.org/).
- [OWASP Secure Headers Project](https://www.owasp.org/index.php/OWASP_Secure_Headers_Project#hsts).