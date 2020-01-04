---
layout: post
title:  "HTTP security headers: Referrer-Policy"
number: 63
date:   2017-09-29 5:00
categories: security
---
Let's say you're visiting Twitter and click on a link in a tweet. When you navigate to the new website, it will know that you came from Twitter, because Twitter and other websites add something called a referrer (usually misspelled 'referer') header. This is how most analytics applications are able to know where your users came from. And while its great to have this information to optimize your website, it might be nice to protect your users privacy as well. The Referrer-Policy header allows several options that can help you control the amount of information you give away as a referrer.

- `no-referrer`: Don't set referrer information, including for pages on your own site.
- `no-referrer-when-downgrade`: Don't send referrer information from HTTPS pages to HTTP pages.
- `same-origin`: Only set referrer information if the origin is the same.
- `origin`: Set the referrer header to the origin of the request, stripping any path information.
- `strict-origin`: Combination of `origin` an `no-referrer-when-downgrade`.
- `origin-when-cross-origin`: Send full URL for pages in the same origin, but work like `origin` for cross-origin requests.
- `strict-origin-when-cross-origin`: Combination of `origin-when-cross-origin` and `strict-origin`.
- `unsafe-url`: Always send full URL anywhere and everywhere. Avoid this one like the plague.

You can add this to Nginx using:

```nginx
add_header 'Referrer-Policy' 'strict-origin';
```

Which header you use should depend on your use-case. Personally, the `strict-origin` policy seems best, because it is a nice combination of protecting users' privacy and being polite to other websites by allowing them to do basic analytics. It would also prevent leaking referrer information in the event of a protocol downgrade.

## Resources
- [OWASP Secure Headers Project](https://www.owasp.org/index.php/OWASP_Secure_Headers_Project#rp).
- [Scott Helme on Referrer-Policy](https://scotthelme.co.uk/a-new-security-header-referrer-policy/).