---
layout: post
title:  "HTTP security headers: X-Frame-Options"
number: 61
date:   2017-09-29 3:00
categories: security
---
Phishing is a popular form of fraud online. The way it works is actually very simple. A hacker will create a domain that looks very similar to a website they're trying to impersonate, and then load the actual target website into their fake one using frames. Once they do this, they can modify certain HTML on the page behind the scenes. What this means is, instead of visiting `myfavoritebank.com`, you could be manipulated into visiting `myfavouritebank.com`, which will contain HTML content that looks exactly like your bank website, but the username and password field will actually submit your credentials to the hacker's own database.

Loading a trusted website in a frame also enables a type of attack known as clickjacking. This means that an attacker could load a normally trusted website, but over the button that says "Login", they could place malicious code that will do something else, tricking you into believing that you're performing just another normal command on a trusted website.

The X-Frame-Options header is designed to indicate to web browsers whether your website is allowed to be loaded in frames or not. It takes several options:
- `deny`: Don't load this website in a frame.
- `sameorigin`: Load this website in frames from the same origin, but nowhere else.
- `allow-from: DOMAIN`: Only load this website in the specified domains.

To enable this in Nginx, add the following header:

```nginx
add_header X-Frame-Options deny;
```

## Resources
- [OWASP on Phishing](https://www.owasp.org/index.php/Phishing).
- [OWASP on Clickjacking](https://www.owasp.org/index.php/Clickjacking).
- [OWASP Secure Headers Project](https://www.owasp.org/index.php/OWASP_Secure_Headers_Project#xfo).