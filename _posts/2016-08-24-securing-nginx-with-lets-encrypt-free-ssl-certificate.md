---
layout: post
title:  "Securing Nginx with Let's Encrypt Free SSL Certificate"
number: 15
date:   2016-08-24 0:00
categories: security
---
Let's Encrypt is a new CA that aims to make it easier for people to encrypt traffic on the web. It provides a simple and automated mechanism for installing and renewing SSL certificates for your web server. And it's all free.

Let's get started.

## Installing A New Certificate
The steps to install a new certificate are pretty simple.

### Step 1
The first step is to stop Nginx using `service nginx stop`.

### Step 2
Next, we download the client that we'll use to generate the certificates. Do:

```
cd ~
git clone https://github.com/letsencrypt/letsencrypt
cd letsencrypt
./letsencrypt-auto certonly --standalone --email YOUR_EMAIL -d YOUR_DOMAIN
```

Add `-d` for all the domains you want to generate the certificates for, including www and non-www versions of your domain.

Make sure to enter a valid email address, as Let's Encrypt can send you notices a few days before your certificate is about to expire.

### Step 3
The above command will put your generated certificates and related files in `/etc/letsencrypt/live/YOUR_DOMAIN`.

### Step 4
Finally, we'll add our certificates to Nginx. Edit your configuration file and add the following lines in the `server` block:

```nginx
listen [::]:443 ssl;
listen 443 ssl;
ssl_certificate /etc/letsencrypt/live/YOUR_DOMAIN/fullchain.pem;
ssl_certificate_key /etc/letsencrypt/live/YOUR_DOMAIN/privkey.pem;
```

### Step 5
Restart Nginx using `service nginx restart`.

And that's it! You should now be able to visit your website over https.

## Auto-renew your certificate
The client you downloaded above is also capable of renewing your certificate automatically. Run the following command:

`/root/letsencrypt/letsencrypt-auto renew`

Right now, since you just installed your certificate, it's not up for renewal, so the command will just print some text confirming that. You can add the command to your cron tab and have your certificate renewed automatically.

```
0 1 * * 0 /root/letsencrypt/letsencrypt-auto renew >> /var/log/letsencrypt-renew.log
15 1 * * 0 service nginx restart
```