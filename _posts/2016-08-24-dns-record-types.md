---
layout: post
title:  "DNS Record Types"
number: 16
date:   2016-08-24 1:00
categories: networking
---
DNS, or the Domain Name System, is what translates human-readable domain names like `ayushsharma.in` to the IP address of the machine which hosts the files and services for that domain.

When you enter a domain name into your browser, it looks up the IP address of the machine where the files for the domain name reside, and serves those files back to you. That's a basic working of DNS, and it's in play every time you browse the web.

DNS has several record types that have different functions, and unless you have your own website and domain name, you'll rarely have to interact with them. In the event that you do, here are the basic record types.

## A Record
It maps a human-readable domain to the IP address of the machine where your files reside.

## AAAA Record
Same as the A record, except this is for IPv6 addresses.

## CNAME Record
Canonical name refers to an alias. It is used to map an alias to a true or canonical domain name. For example, `www.ayushsharma.in` maps to `notes.ayushsharma.in`. You can use it to map one sub-domain to another.

## NS Record
Name server records point to the servers that host DNS information. You'll generally have primary and secondary name server records.

## MX Record
Mail exchanger records determine where to send the email sent to your domain.

## TXT Record
This is a generic record, and can contain human-readable or machine-readable data used for a number services. It is useful for proving ownership of the domain, or for implementing security protocols such as SPF and DKIM.

## TTL
TTL, or time-to-live, determines how many seconds the name server caches your information for.