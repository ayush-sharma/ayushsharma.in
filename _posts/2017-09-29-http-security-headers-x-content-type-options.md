---
layout: post
title:  "HTTP security headers: X-Content-Type-Options"
number: 62
date:   2017-09-29 4:00
categories: security
---
At some point in the ancient past, people generally refused to set proper MIME-type headers for their content. To address this problem, web browsers thought it was a good idea to get smart and try to figure out the MIME-type by processing the byte stream. This opened up a security vulnerability, which could allow an attacker to trick the browser into executing malicious content while it was sniffing it. Imaging uploading a lot of image files to a website, and while the browser would try to sniff the images to figure out what they were, code hidden within these images would execute and cause all sorts of trouble. This is called a cross-site scripting (XSS) attack. And so a well-meaning feature turned into a security bug. This new X-Content-Type-Options header is designed to turn off that original well-meaning feature.

This header only has one option:
- `no-sniff`: No sniffing.

Adding this header to Nginx is straighforward:

```nginx
add_header X-Content-Type-Options nosniff;
```

## Resources
- [Content-sniffing on Wikipedia](https://en.wikipedia.org/wiki/Content_sniffing).
- [OWASP Secure Headers Project](https://www.owasp.org/index.php/OWASP_Secure_Headers_Project#xcto).