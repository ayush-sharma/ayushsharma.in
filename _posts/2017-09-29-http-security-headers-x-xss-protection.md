---
layout: post
title:  "HTTP security headers: X-XSS-Protection"
number: 59
date:   2017-09-29 1:00
categories: security
---
This is a header designed to indicate to web browser what it should do with its built-in XSS protection filters. This header takes 3 simple values:

- `0`: Disable the browser's filter.
- `1`: Enable the browser's filter.
- `1; mode=block`: Enable the browser's filter, but block the page from loading instead of trying to sanitize it.

This setting is useful when you don't know what the user may have set their browser's XSS filter setting to. The last option is considered best practice, but whatever you choose based on your use-case, don't rely on the setting on the user's end. Configure the setting you want using this header.

To use this setting in Nginx, we'll add this:

```nginx
add_header X-XSS-Protection "1; mode=block";
```

## Resources
- [The misunderstodd X-XSS-Protection](http://blog.innerht.ml/the-misunderstood-x-xss-protection/).
- [OWASP Secure Headers Project](https://www.owasp.org/index.php/OWASP_Secure_Headers_Project#xxxsp).