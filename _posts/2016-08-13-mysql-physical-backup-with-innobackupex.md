---
layout: post
title:  "MySQL Physical Backup with Innobackupex"
number: 8
date:   2016-08-13 3:00
categories: databases
---
The MySQL documentation ([http://dev.mysql.com/doc/refman/5.6/en/backup-types.html](http://dev.mysql.com/doc/refman/5.6/en/backup-types.html)) has a very nice summary of what physical and logical backups are for. Physical backups consist of copies of the actual database files themselves, which makes this method fast but not very portable, but it is still recommended for your important databases. Today we'll be doing physical backups using Percona Xtrabackup.

## Installation
You can install Percona Xtrabackup from here:
<br>
[https://www.percona.com/doc/percona-xtrabackup/2.4/installation/apt_repo.html](https://www.percona.com/doc/percona-xtrabackup/2.4/installation/apt_repo.html)

We'll be using a test database for this which has a lot of random float values in it.

## Backing Up
To back up your existing MySQL data, run the following command:

`innobackupex --defaults-file=/etc/mysql/my.cnf  --user=root --databases="test mysql" BACKUP_DIR`

The command will take all your database files from the MySQL data directory to `BACKUP_DIR`. Note that the command might create a time stamped directory inside the target directory. You can check the directory for the database files.

## Restoring
Now let's try restoring the backup we just made. First we'll have to delete the database files (I hope you're not doing this on production!). Like so:

```
service mysql stop
rm -rf /var/lib/mysql/*
```

Before we can restore the backup, we need to prepare it:

`innobackupex --apply-log BACKUP_DIR`

And then:

```
cp -r BACKUP_DIR/* /var/lib/mysql/
chown -R mysql:mysql /var/lib/mysql
service mysql start
```

And that's it! Everything should have gone fine, and your previous database should be there now.

Note that you'll also have to back up the default `mysql` database or it will complain about users and plugins table missing, among other things.