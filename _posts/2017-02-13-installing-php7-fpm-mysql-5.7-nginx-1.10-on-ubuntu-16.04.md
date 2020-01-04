---
layout: post
title:  "Installing PHP 7 FPM + MySQL 5.7 + Nginx 1.10 on Ubuntu 16.04"
number: 28
date:   2017-02-13 0:00
categories: development
---
These are instructions for installing PHP7, MySQL 5.7 and Nginx 1.10 on Ubuntu 16.04.

## Update All The Things
First run:

```
apt-get update
```

## Install MySQL

```
apt-get -y install mysql-server
```

Check version using:

```
mysql --version
mysql  Ver 14.14 Distrib 5.7.17, for Linux (x86_64) using  EditLine wrapper
```

## Install Nginx

```
apt-get -y install nginx
```

Check version using:

```
nginx -v
nginx version: nginx/1.10.0 (Ubuntu)
```

## Install PHP-FPM

```
apt-get -y install php7.0 php7.0-fpm php7.0-mysql php7.0-curl php7.0-json php7.0-cli php7.0-common php7.0-xml
mkdir -p /run/php
```

Check version using:

```
php -v
PHP 7.0.13-0ubuntu0.16.04.1 (cli) ( NTS )
Copyright (c) 1997-2016 The PHP Group
Zend Engine v3.0.0, Copyright (c) 1998-2016 Zend Technologies
    with Zend OPcache v7.0.13-0ubuntu0.16.04.1, Copyright (c) 1999-2016, by Zend Technologies
```

## Configure Nginx
Change the config in `/etc/nginx/sites-enabled/default` to:

```nginx
server {
    listen 80 default_server;
    listen [::]:80 default_server ipv6only=on;

    server_name     localhost;
        
    set $root_path '/var/www/html';

    root $root_path;
    index  index.php index.html index.htm;

    try_files $uri  $uri/   /index.php;

    access_log      /var/log/nginx/access.log;

    location ~ \.php$ {
            try_files $uri =404;
            fastcgi_pass unix:/run/php/php7.0-fpm.sock;
            include fastcgi_params;
            fastcgi_index index.php;
            fastcgi_split_path_info       ^(.+\.php)(/.+)$;
            fastcgi_param PATH_INFO       $fastcgi_path_info;
            fastcgi_param PATH_TRANSLATED $document_root$fastcgi_path_info;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            include fastcgi_params;
            fastcgi_read_timeout 15;
    }
        
    location ~ /\.ht {
        deny all;
     }
}
```

## Restart Services
Do:

```
service mysql restart
service nginx restart
service php7.0-fpm restart
```

## Test Installation
You should see the PHP information page after running the following:

```
echo "<?php phpinfo(); ?>" > /var/www/html/index.php
curl http://localhost
``` 

## Final One-shot script
You can run all the commands in one go. Paste the following in one file and run it:

```
apt-get update
apt-get -y install mysql-server
apt-get -y install nginx
apt-get -y install php7.0 php7.0-fpm php7.0-mysql php7.0-curl php7.0-json php7.0-cli php7.0-common php7.0-xml
mkdir -p /run/php
echo "
server {
    listen 80 default_server;
    listen [::]:80 default_server ipv6only=on;

    server_name     localhost;
        
    set \$root_path '/var/www/html';

    root \$root_path;
    index  index.php index.html index.htm;

    try_files \$uri  \$uri/   /index.php;

    access_log      /var/log/nginx/access.log;

    location ~ \.php$ {
            try_files \$uri =404;
            fastcgi_pass unix:/run/php/php7.0-fpm.sock;
            include fastcgi_params;
            fastcgi_index index.php;
            fastcgi_split_path_info       ^(.+\.php)(/.+)\$;
            fastcgi_param PATH_INFO       \$fastcgi_path_info;
            fastcgi_param PATH_TRANSLATED \$document_root\$fastcgi_path_info;
            fastcgi_param SCRIPT_FILENAME \$document_root\$fastcgi_script_name;
            include fastcgi_params;
            fastcgi_read_timeout 15;
    }
        
    location ~ /\.ht {
        deny all;
     }
} " > /etc/nginx/sites-enabled/default

echo "<?php phpinfo(); ?>" > /var/www/html/index.php

service mysql restart
service nginx restart
service php7.0-fpm restart

curl http://localhost
```

That's all, folks!