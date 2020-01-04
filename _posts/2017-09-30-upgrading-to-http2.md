---
layout: post
title:  "Upgrading to HTTP/2"
number: 64
date:   2017-09-30 0:00
categories: security
---
Tim Berners-Lee began the internet revolution in 1989 when he invented the HTTP protocol, allowing computers to talk to each other using relatively slow computers. As the needs to the internet increased, the protocol got a refresh in 1992 as HTTP/1.1. But even this refresh was not sufficient, since web technologies got crazier and more demanding. Increase in CPU speeds and graphics technology allowed developers to build newer-fangled applications, but the old HTTP/1.1 had its limitations. If you've been developing for the web for some time, the limitations/hacks below will be familiar:

- Domain sharding: Since browsers over HTTP/1.1 are only capable of downloading one resource from one domain at a time, the recommended method to allow the browser to use multiple connections was something called domain sharding. The idea was to split the resources you need for your application into multiple domains, like `css1.example.com/main.css` and `css2.example.com/base.css`. While this allowed parallel loading of resources, it increased deployment complexity for applications. It also increased round-trip time for these resources.
- Image sprites: This practise involved combining multiple images, like icons and smaller graphics, into one large image file, and then using positional directives in CSS to pick an image to display. This reduced the number of images to download, but increased development complexity.
- Combining CSS and JS files: A lot of pre-processors out there supported development using multiple CSS and JS files for ease of development, and then combining them during a "build" phase into a single large file. Since not all CSS and JS were necessary on a single page, this required some creative organisation of CSS and JS to ensure only the required style and scripts are available on any given page.
- Inlining: The limitation in downloading CSS and JS in parallel encouraged inlining as much as possible, but this increased the page download time and delayed page rendering until it was fully downloaded.
- Cookieless domains: Static resources like images, CSS and JS were recommended to be served from a cookieless domain to reduce the overhead needed in processing cookies for these resources, since they don't require them.

HTTP/2 is the latest upgrade to the HTTP protocol, published by the Internet Engineering Task Force (IETF) in 2015. While the new version retains some of the high-level functionality like methods, status codes, etc., a lot has changed under the hood. The limitations of HTTP/1.1 listed above are now gone. HTTP/2:

- Is binary instead of textual. This makes it easier for the server to parse information and makes it more compact and less error-prone. No extra time is needed to translate text to binary, which is the computer's native language.
- Is fully multiplexed, meaning HTTP/2 can send multiple requests for data in parallel over a single connection. This reduces round-trip time, reducing the time it takes for a website to load.
- Uses header compression HPACK to reduce overhead.
- Allows servers to “push” responses proactively into client caches instead of waiting for a new request for each resource.
- Uses the new ALPN extension which allows for faster encrypted connections since the application protocol is determined during the initial connection.
- Eliminates the need for domain sharding and asset concatenation.

This means that all that time and effort we spent speeding up our websites to get over the inherent flaws of HTTP/1.1 are no longer needed.

According to [caniuse.com](https://caniuse.com/#feat=http2), the browser support for the new protocol is about 50% in India and 82% globally. Whether your users' browsers will support this protocol or not will depend on their browser version, which you can check using your analytics tool, like Google Analytics. Also note that while HTTP/2 supports both secure and non-secure connections, both Mozilla Firefox and Google Chrome will only support HTTP/2 over HTTPS. Unfortunately, this means that many sites that want to take advantage of HTTP/2 will need to be served over HTTPS, which is not necessarily a bad thing. You should have switched to HTTPS by now anyway, [especially since the server certificates are free]({% post_url 2016-08-24-securing-nginx-with-lets-encrypt-free-ssl-certificate %}).

HTTP/2 maintains backwards compatibility with HTTP/1.1, which means you can make the switch and your applications will still work on the older HTTP/1.1. Enabling HTTP/2 support in Nginx is pretty trivial. Just make the following changes to your secure server definition:

```nginx
listen 443 ssl http2;
listen [::]:443 ssl http2;
```

And that's it! Just make sure your HTTPS certificate configuration is correct, and supported browsers should begin talking to your server using the new HTTP/2. You can check this in your Chrome's Developer Tools' Network tab. Find the "Protocol" column, and it should say `h2` for the new HTTP/2, and `http/1.1` for the older HTTP/1.1.

## Resources
- [HTTP/2 FAQ](https://http2.github.io/faq/).
- [HTTP/1.1 vs HTTP/2 demo](http://www.http2demo.io/).