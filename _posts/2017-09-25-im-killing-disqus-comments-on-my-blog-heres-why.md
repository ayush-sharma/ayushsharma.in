---
layout: post
title:  "I'm killing Disqus comments on my blog. Here's why."
number: 57
date:   2017-09-25 0:00
categories: privacy
---
## Introduction
In order to start figuring out how to speed up and optimise this website, I thought I would start by using some popular website speed test tools to figure out the current situation, and go from there. Two of the very popular ones out there are GTMetrix and Pingdom. So I chose one page URL from my blog, and used the tools to see what improvements were recommended. And that’s when I found out that Disqus was loading a bunch of junk ad-tracking domains behind the scenes.

## GTMetrix raised some red flags...
I started with GTMetrix. It reported a horrible score, and recommended that I minimise redirects and leverage browser caching, among other things.

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}disqus-comments-ad-tracking-gtmetrix.jpg" width="700" height="254" alt="Disqus comments adding ad-tracking domains to websites. This is a test using GTMetrix.">

## … and so did Pingdom
Running tests with Pingdom didn’t get any better. It too recommended minimising redirects and leveraging browser caching.

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}disqus-comments-ad-tracking-pingdom.jpg" width="700" height="390" alt="Disqus comments adding ad-tracking domains to websites. This is a test using Pingdom.">

Now, this is a fairly simple blog: it uses static HTML compiled using Jekyll, some images, CloudFlare CDN, Google Analytics, Google Fonts, and the usual social connect buttons. That’s about it. None of this should account for over 100 requests, not to mention the insane amount of redirects, etc.

Diving down into the redirects, I started seeing a lot of this:

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}disqus-comments-ad-tracking-domains.jpg" width="700" height="547" alt="Disqus comments adding ad-tracking domains to websites. This is a list of domains I found on my own blog.">

## The ad-tracking URLs
Pingdom helpfully allowed me to download the above report as JSON, and I extracted all of the domains that occurred in the report. Here is the list:

```
aa.agkn.com
accounts.google.com
apis.google.com
beacon.krxd.net
c.disquscdn.com
c.disquscdn.com
cdn.krxd.net
cdn.viglink.com
cm.dsp.linksynergy.com
cm.g.doubleclick.net
connect.facebook.net
cts.w55c.net
d.agkn.com
disqus.com
disqus.com
dpm.demdex.net
e.nexac.com
ei.rlcdn.com
fonts.googleapis.com
fonts.gstatic.com
glitter.services.disqus.com
googleads.g.doubleclick.net
gpush.cogocast.net
i.liadm.com
ib.adnxs.com
idsync.rlcdn.com
io.narrative.io
links.services.disqus.com
loadus.exelator.com
match.adsrvr.org
notes.ayushsharma.in
p.adsymptotic.com
personalnotes.disqus.com
pippio.com
pixel.sojern.com
pixel.tapad.com
pm.w55c.net
rc.rlcdn.com
referrer.disqus.com
s.cpx.to
sp.adbrn.com
ssl.google-analytics.com
ssl.gstatic.com
stags.bluekai.com
staticxx.facebook.com
stats.g.doubleclick.net
sync.mathtag.com
tag.apxlv.com
tag.cogocast.net
tags.bluekai.com
usermatch.krxd.net
www.facebook.com
www.google-analytics.com
x.bidswitch.net
x.dlx.addthis.com

```

If you browse through the list above, you’ll be able to quickly identify the few domains that actually belong on that list. There’s the domain for this blog, for Disqus comments, for Google Analytics, Google Fonts… and that’s about it. The rest of them are all ad-tracking domains. But the domains weren’t enough, because they came with their assortment of cookies as well. And since these cookies were placed by Disqus’s Javascript, which I had already placed in my code, they became authorised as well. Feels like I got Trojan-Horse-ed. This sneakily gets around the [Same-origin policy](https://en.wikipedia.org/wiki/Same-origin_policy) and essentially becomes a [Cross-site scripting attack](https://en.wikipedia.org/wiki/Cross-site_scripting). The difference becomes important when I trust Disqus not to load third-party scripts on my site behind my back. Which I don’t. Not anymore.

## So… why is this happening?
This is happening because Disqus, "the web’s largest network of discussion communities”, and Xaxis, "the world’s largest programmatic media and technology platform”, decided to team up in 2014 to use the comments you leave using Disqus on any website to build anonymous profiles on you and target ads and sponsored stories. This is great news, because, you see, "Creative possibilities for brands include text, images and embedded video, tailored to specific audiences, topics under discussion, or both”. Of course, as a corporation, you might be worried that your information might be placed on websites where there are comments which might be damaging to your brand, but fear not, because "The platform will continually scan the words and expressions used on sites and in comments to ensure ads are only placed in brand safe environments.”. And so the words you leave behind, even outside the walled gardens of social media websites, are now open to be monetised. Reminds of that one episide from [Black Mirror](https://en.wikipedia.org/wiki/Black_Mirror).

I don’t know how you feel about ad-tracking technology. Personally, I think its a little creepy. So I’m ditching Disqus comments on this blog until there is a safer alternative. There is an [opt-out option](https://www.xaxis.com/static/view/opt-out-confirmation), but that only works for users who know there is one. Maybe I should place it in the footer of the blog, warning users that ad-tracking is on.

But that’s a conversation for later. For now, I’m nuking the comments section.

## Resources
- [Chris Lema: Why I killed Disqus Commenting on my site](http://chrislema.com/killed-disqus-commenting/).
- [Disqus launches advertising based on commenting history](https://www.businessinsider.com.au/disqus-launches-advertising-2014-11).
- [Xaxis and Disqus partner to read your comments and target ads](https://www.xaxis.com/press/view/xaxis-and-disqus-introduce-first-global-programmatic-platform-for-native-ad).