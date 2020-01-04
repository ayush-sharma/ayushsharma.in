---
layout: post
title:  "You still might need a staging server in 2017"
number: 32
date:   2017-03-17 0:00
categories: reliability
---

If you're someone whose about to hire a freelancer for some work, you need to read this.

If you're a freelancer about to do some work for a client, you need to read this.

If you're a company about to deploy a new feature to production, you need to read this.

Years ago when I was freelancing, one of the first projects I ever did was to design a website. The requirements were very simple and it seemed like an opportunity to make some easy money. I had fun developing the layout, writing the backend code, organising everything until it was crisp and neat. I know more about those processes now than I did back then, but the idea of taking a simple problem statement from a complete stranger and delivering something that would ease their life was a very powerful thing for me. And it still is. After several weeks of nurturing and raising my first project, I was finally ready to deliver.

I got the SFTP credentials of the shared hosting provider from my client. By then I had already worked with SFTP, so all I had to do was use FileZilla to open up a connection to the host, right-click the files on my computer, and select Upload. And the files just went, travelling over unseen wires, perhaps traversing continents, until they reached their final destination.  Once the upload was complete, I hit the website URL in my browser to see my work, and all I saw was a glaring PHP error, telling me that something had gone horribly wrong.

After some diagnosis I found out that the hosting provider was using a much older version of PHP, and I had developed the project in the latest, shiniest version. And why wouldn't I? Not only do I have a duty to build applications in the most secure software version possible, I also have a duty to myself to play around with the latest toys every chance I get. So of course it was the hosting providers fault. Who would use such an old version of PHP? Don't they know they need to update their software regularly? Screw them for living in the past, and not offering better choices to their customers, which includes me, the developer, more than it includes my client. Right?

So it's settled. My client would have to move to a hosting provider who would have the latest version of PHP so that my website can run. I mean his website. After some hunting around, I found out that all major hosting providers at the time were still on older versions of PHP, and if I needed the one I wanted, I would have to use VPS hosting and set up my own server, which was a daunting task for me back then.

I guess I would have to tell me client that their hosting provider screwed them, and that my shiny and glitzy website would not see the light of day unless we shifted gears. I'd have to convince him that we would have to move to VPS hosting. Setting up a server couldn't be that hard. I could just Google things and surely someone will have written a guide. It might take a while and some trial and error, but its for the greater good. My client would have to understand.

What I failed to realise during all this was that while I was cursing the hosting provider for living in the past, my client's production website was down, and he was losing money and customers.



Developers tend to see truth in software. And even when we don't, we tend to gravitate towards keeping software up to date just because, even if we don't understand the release log all that much. Latest is best, and that's how it is. But if you're a non-technical person just looking to have a presence on the web, do you care that your website doesn't run because your developer used the latest array syntax which won't run on older versions? You just care that your website does't run. And your website is not running right now because developers are humans, and sometimes we're stupid humans. As we start our careers we tend to make the same mistakes scores of developers have made in the beginning of their own careers, and as we progress we don't always learn from our mistakes. Which means that our clients end up paying for them. And that is wrong.

This whole situation could have been avoided if we had a staging environment in place.

The idea of a staging environment is very simple. You deploy your website/application to a test area first and review it before going live with. Which means that if something is going to break, it's going to break in staging and not in production. It gives us a chance to review everything before risking it all. For this to work, the staging area and the production area have to be exactly alike. Even if there is a little difference in the two it will introduce a variation that will make staging moot. The big things are important, like PHP versions and database versions, but the little things matter too. In the case of my client, we could have uploaded the files to a separate sub-domain (like staging.example.com) on the same account, and reviewed the changes before going live. That would have told us what was wrong without risking a production outage. Staging changes before going live also gives developers more confidence when working on projects, and gives us a chance to fix things before breaking production.

The idea of a staging environment is very simple, but people do it in different ways. You might have a separate hosting account to test changes, or a separate machine, or a separate cluster, depending on your scale. The idea is to have an identical copy of the production environment and reduce variations between them. The more alike they are, the better the chances of finding issues and faults early on.

Whether you're an individual about to hire a freelancer for a website so you can sell your wares online, or a Fortune 500 company working on the next big thing, you need a staging environment to have a safe place to see if things break before breaking something important.

A staging workflow would look this this:

- Development: This is the developer's laptop or computer. All development happens here. We write our code, build our website, and test our features.
- Staging: Once we're done and are ready to show something to our clients, we would deploy our work here. This could be a separate folder or sub-domain on the shared host. We would check if everything is working properly, checking the layout, the links, etc. It's also a good idea to deliberately break things, and then fix them in code, because now we can do it safely.
- Production: Once everything looks fine and the client is satisfied, we would move our code from staging to production.

This workflow allows us to go from "code runs on developers laptop" to "code runs on main website", by taking the intermediate and important step of "code runs on staging".

So let's do a quick recap.

A staging environment prevents:

- Developers and hosting providers from blaming each other.
- Surprises.
- Downtime.
- Mistakes.