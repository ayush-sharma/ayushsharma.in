---
layout: post
title:  "HTTP security headers: Content-Security-Policy"
number: 58
date:   2017-09-29 0:00
categories: security
---
For a long time, web browser security was based on the Same-origin policy. What this policy says is that since web browsers implicitly trust any content that shows up from a requested domain, they will block requests from within this content to load scripts on other domains. But there have been ways around this, [as I recently found out]({% post_url 2017-09-25-im-killing-disqus-comments-on-my-blog-heres-why %}), via cross-site scripting (XSS) attacks. In a typical XSS attack, an attacker can use a medium you usually trust to inject malicious code which the browser might be forced to execute. CSP was designed to prevent this from happening.

The CSP header is a no-nonsense header that is designed to help you define a whitelist of trusted scripts and domains. The browser will then only load resources from these trusted domains, while blocking requests from others. CSP also provides a mechanism to provide a URL where the browser will report policy violations, so you can keep track of where malicious content is coming from and take steps accordindly. This allows you to debug your CSP policies and identify new and emerging threats. CSP will also block inline Javascript, Javascript with `<script></script>` tags, and `eval()`s.

Currently, this header has the following options:
- `default-src`: This is the default policy. Applies as a fallback in the event other policies are not explicitly defined. It is recommened to keep this policy tight, and relax controls in the other policies.
- `script-src`: Define which scripts the protected resource can execute.
- `object-src`: Define from where the protected resource can load plugins.
- `style-src`: Define which styles (CSS) the user applies to the protected resource.
- `img-src`: Define from where the protected resource can load images.
- `media-src`: Define from where the protected resource can load video and audio.
- `frame-src`: Define from where the protected resource can embed frames.
- `font-src`: Define from where the protected resource can load fonts.
- `connect-src`: Define which URIs the protected resource can load using script interfaces.
- `form-action`: Define which URIs can be used as the action of HTML form elements.
- `sandbox`: Specifies an HTML sandbox policy that the user agent applies to the protected resource.
- `script-nonce`: Define script execution by requiring the presence of the specified nonce on script elements.
- `plugin-types`: Define the set of plugins that can be invoked by the protected resource by limiting the types of resources that can be embedded.
- `reflected-xss`: Instructs a user agent to activate or deactivate any heuristics used to filter or block reflected cross-site scripting attacks, equivalent to the effects of the non-standard X-XSS-Protection header.
- `report-uri`: Specifies a URI to which the user agent sends reports about policy violation.

An example CSP policy in Nginx might look like this:
```bash
add_header Content-Security-Policy "default-src 'none'; script-src 'self'; style-src 'self'; img-src 'self'; child-src 'self'";
```

Let's try this out.

Apply the following CSP policy to your server block:

```nginx
add_header Content-Security-Policy "default-src 'none'; script-src 'self'; style-src 'self'; img-src 'self'; child-src 'self'";
```

Then, upload the following HTML file to your Nginx document root:

```html
<html>
<head>
    <script src="myjs.js"></script>
    <script src="https://localhost:444/myjs.js"></script>;
    <script src="https://evil.domain.com/myjs.js"></script>;
</head>
<body>
    Hello, World!
    <script>
        alert("This is some evil stuff...");
    </script>
</body>
</html>
```

I'm using this test setup with SSL on port 444, but yours will be different. Also, create and empty Javascript file by the name of `myjs.js` in the same folder.

Once this is done, try viewing the page using `https://` in your browser. In Chrome's developer tools, in the `Console` section, you should see this:
<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}http-security-headers-content-security-policy-blocked-by-csp-console.jpg" width="700" height="31" alt="HTTP security header Content-Security-Policy Console tab output in Chrome.">

If you see above, there are two policy violations: one for the Javascript we tried to load from evil.domain.com, and the other from the inline `<script></script>` we tried to load in the page. CSP correctly blocked both. If you need to allow inline Javascripts, then you need to specify `unsafe-inline` in your CSP policy. This will allow inline Javascript to execute, which is not very secure, but it will allow you to have CSP control other security aspects while you figure out how to remove inline Javascript from your code. Which is a good idea.

If you also see the `Network` tab in developer tools, you'll see that the Javascript from the `evil.domain.com` was blocked, and the reason was clearly listed as `blocked:csp`.
<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}http-security-headers-content-security-policy-blocked-by-csp-network.jpg" width="700" height="60" alt="HTTP security header Content-Security-Policy Network tab output in Chrome.">

There is a lot more to read on CSP, including on how you can relax some of its controls to block a few things but allow some others while you're testing out a new CSP. It's reporting feature is especially useful if you want to capture policy violations on your end to debug potential policy misconfigurations and to know when bad guys are trying to infiltrate or probe your system.

## Resources
- [Official W3C CSP specification](https://www.w3.org/TR/CSP/).
- [Google web fundamentals on CSP](https://developers.google.com/web/fundamentals/security/csp/).
- [CSP quick reference](https://content-security-policy.com/).
- [OWASP Secure Headers Project](https://www.owasp.org/index.php/OWASP_Secure_Headers_Project#csp).