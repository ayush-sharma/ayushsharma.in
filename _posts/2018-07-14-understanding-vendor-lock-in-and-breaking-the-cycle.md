---
layout: post
title:  "Understanding vendor lock-in and breaking the cycle"
number: 68
date:   2018-07-14 0:00
categories: cloud
---
I have a very ordinary problem. And if you have a computer, or two, you must face it too.

I have an Ubuntu workstation that I love. I use it for most of my work. I also have a 2016 MacBook Pro that has become my mistress. And for not-so-obvious reasons, they both hate each other.

And so this is what I have to contend with on a daily basis.

## The Cycle
I want to check my email. Sounds simple, right? Not really. I bought the extremely well-designed Airmail on my Mac to check my email. But there is no Airmail for Ubuntu. Ubuntu has Thunderbird. And Thunderbird is fugly. Most Ubuntu apps are fugly. We don’t say it because we all love open-source, and we appreciate the time and effort the community puts in so we can have our cake and eat it too without any effort on our part, but while developers have spent countless hours building Ubuntu into a solid and stable operating system, it has been completely ignored by designers. As far as I can tell. I’m using Ubuntu 18.04, and switching to a Mac is a breath of fresh air. But that’s okay, because ProtonMail causes Airmail to crash for no reason, which is great because that way my email is really secure on my Mac because I can’t see any of it. See? If someone steals my Mac, they can’t access my email. Because I can’t access my email. Also, I can’t use it on Ubuntu either, so that solves that problem. They do have a web app thing. But I don’t want to use the web app thing.

I want to share files. Sounds simple, right? Not really. Google Drive works on Mac, but not on Ubuntu. It does have native integration with Nautilus, but that only works while you have internet. I used to use DropBox, but since their IPO its like they don’t even know me. I was once locked out of my account for seven days. I don’t have DropBox anymore. I tried SpiderOak, which scanned the 10TB of hard disk space I have on Ubuntu and told me that it was ready to upload 262TB. I don’t have SpiderOak anymore. I could try Microsoft’s OneDrive, but another mistress would really make things crazy. They all have web app things. But I don’t want to use the web app things.

I want to store passwords. Sounds simple, right? Not really. I have 1Password. It’s amazing. On Mac. There is no native client for Ubuntu. They have a web app thing. But I don’t want to use the web app thing.

I want to play games. Sounds simple, right? Not really. I have Steam. I love Steam. It has all the games that I can’t play on either Ubuntu or Mac. The Ubuntu games are limited, although Transistor was awesome. The Mac games are less limited. Most of them are for Windows, but another mistress would really make things crazy. And if you can’t play Skyrim, does any of it really matter? There is no Skyrim on Ubuntu or Mac. So there is no point.

You may by now already have a list of open-source things or other that I could use to solve the above challenges. But the problem with my situation is not that I don’t have technology, it’s that I have too much technology, and another technology in the mix is just going to cause more pain. The situation above is caused by a little known malady called vendor lock-in. I thought I would spend some time to understand a bit more about it before I started “solving” things.

## What basic necessities do we need from a vendor?
If we’re considering a particular vendor for file sharing or email, we usually look at the following things:

- Survivability: Is the vendor new or has it been in the game for long? Will they stick around? Are they stable in the long run? If not, what is the cost of migration to a competitor?
- Resilience: Have they had issues with uptime in the past, and how frequently? Are there any known and on-going issues or technical glitches? How do they handle security breaches?
- Price: What is their price now? If we choose a free plan, is it free forever or will we have to pay later? Is it freemium? What is the cost of migration to a competitor, just in case?
- Innovation: Does the service require new features? If so, how rapid is their pace of development? Committing to new features from a vendor will increase lock-in.
- Openness: Are they truly open-source, or rely on proprietary customization of open-source software?

Lock-in is defined as a reduction in possible options during a service disruption. In the scenario that one or more of the above needs can no longer be met, is it easy to recover data and switch to another vendor? If not, then we are locked-in. 

## How does lock-in happen?
There are many ways in which we can get locked-in to a product or service. For example:
- Vendors usually have different implementations for doing the same thing, so switching vendors once chosen is not always easy. 
- Using a vendor for one service increases the temptation for using others. If I use Google for email, why not try Google Drive too? This increases lock-in, and causes all of the original symptoms.
- DIY solutions have their own lock-in. You might be thinking, why not just build your own solution? What about some simple cron jobs for file syncing? The problem is that this would cause lock-in too, because we would be locked-in to the tools we use, and that would cause all of the original symptoms.
- Data has gravity, and moving data around is easier said than done. Have you ever tried moving from Facebook to Google Plus?

## So, how do we avoid lock-in?
Short answer? We can’t. We’re always locked in somewhere, even with DIY solutions there are limitations. So the trick is to contain where to be locked-in. There are usually 2 strategies:

1. Cautious approach: We could choose highly portable or DIY solutions and choose points of lock-in carefully. We would need an escape plan in case things don’t work out.
2. All-in: We could pick a vendor and commit fully. This approach has maximum lock-in but is also the most rewarding, since we could use all of the capabilities of a differentiated platform. For example, comparing DropBox and Google Drive is not really fair, because if we go all-in with Google, then there are other apps that we could make use of, like email, messaging, etc. More lock-in, but more rewards.

Given the brief homework above, finding a way out of my technology hellscape will not be easy. More choices will only compound the problem, and I’ve been eyeing the new Microsoft Surface 2 with much interest. But defining the problem has been a small victory, and that’s enough for now.

I guess it was a good idea to join [Mastodon](https://mastodon.technology/@ayushsharma22). At least I’m not on Facebook.