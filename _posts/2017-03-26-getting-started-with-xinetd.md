---
layout: post
title:  "Getting Started with Xinetd"
number: 37
date:   2017-03-26 0:00
categories: automation
---
Xinetd is a super-server daemon that stands for "Extended Internet Daemon". It's a super-server because it provides access to other servers. It was designed to conserve system resources that would normally require multiple servers that stay idle most of the time. Given that's its simple to install, lightweight, and supports access-controls, I end up using it in a variety of situations. One of the most common use-cases for me is to use it to set up health checks in AWS EC2 for elastic load balancers. You can just run Xinetd, point it to, say, a PHP file, that will run all your health checks and return a HTTP response, which will be returned to the ELB. The advantage is that I get to configure health checks for a variety of services and not rely on those services themselves. It can be configured to health check pretty much whatever I want.

Let's do a basic tutorial to get you started. We're going to be doing this on a Vagrant machine running Ubuntu.

## Installing Xinetd
Installing Xinetd is pretty simple. Just run:

```bash
apt-get install xinetd
```

Once the installation is complete, have a look at `/etc/xinetd.conf`, and you will find that it includes files in `/etc/xinetd.d`. Go to `/etc/xinetd.d` and you can see a lot of sample configurations.

## Testing It Out
Create a new file called `/etc/xinetd.d/ayush` and add the following in it:

```bash
service ayush
{
        log_type                = syslog
        log_on_success          = PID HOST DURATION USERID EXIT
        log_on_failure          = ATTEMPT HOST USERID
        socket_type             = stream
        protocol                = tcp
        wait                    = no
        server                  = /usr/bin/php
        user                    = ubuntu
        only_from               = 127.0.0.1
        server_args             = /tmp/ayush.php
        log_on_success          += DURATION
        nice                    = 10
        disable                 = no
}
```

Here we're telling Xinetd to run the `/tmp/ayush.php` file using PHP whenever a call comes in.

Now add the following line at the end of `/etc/services`:

```bash
ayush 60321/tcp
``` 

Here we're declaring the port and protocol for our new service.

Let's create our `/tmp/ayush.php` file. Add the following content in it:

```php
<?php
$response="Hello, World!";

echo"HTTP/1.1 200 OK" . PHP_EOL;
echo"Content-Type: text/plain" . PHP_EOL;
echo"Connection: close" . PHP_EOL;
echo"Content-Length: ".strlen($response) . PHP_EOL;
echo PHP_EOL;

echo $response;
```

Restart Xinetd using:

```bash
service xinetd restart
```

Now run `curl http://localhost:60321`, and you should see

```bash
Hello, World!
```

And that's it!

What we just did was pretty simple. We created a new service for Xinetd in its configuration directory. We called the service `ayush`, but you can call it whatever you want. We create an entry for the same service in `/etc/services`, where we define the TCP port the service will use. In the configuration, we specified that it will execute a PHP file when called, which we then defined in `/tmp/ayush.php`. In the PHP file, remember to send the right headers and include a line-break or you might run in to the dreaded `curl: (56) Recv failure: Connection reset by peer`, which is still unresolved. But that's no reason to give up on this awesome tool.

Have questions? Use the comments section below.

## Resources
- [Xinetd Wikipedia](https://en.wikipedia.org/wiki/Xinetd)
- [https://www.linuxjournal.com/article/4490](https://www.linuxjournal.com/article/4490)
- [Xinetd MAN page](https://linux.die.net/man/8/xinetd)