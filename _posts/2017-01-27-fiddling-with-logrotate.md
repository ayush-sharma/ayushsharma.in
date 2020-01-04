---
layout: post
title:  "Fiddling with Logrotate"
number: 26
date:   2017-01-27 0:00
categories: monitoring
---
Logs are great when you want to find out what an application is doing, or troubleshoot a possible problem. Almost every application we deal with generates logs, and we want the applications we develop ourselves to generate them too. The more verbose the logs, the more information we have. But left to themselves, logs can grow to an unmanageable size, and they can in-turn become a problem of their own. So it's a good idea to keep them trimmed down, keep the ones we're going to need, and archive the rest.

## Basics
The `logrotate` utility is great at managing logs. It can rotate them, compress them, email them, delete them, archive them, and start fresh ones when you need.

Running `logrotate` is pretty simple: just run `logrotate -vs state-file config-file`.
In the above command, the `v` option enables verbose mode, `s` specifies a state file, and the final `config-file` mentions the configuration file, where you specify what you need done. 

## Hands-on
Let's check out a `logrotate` configuration that is running silently on our system, managing the wealth of logs we find in the `/var/log` directory. Check out the current files in that directory. Do you see a lot of `*.[number].gz` files? That's what `logrotate` is doing. The configuration file for this can be found under `/etc/logrotate.d/rsyslog`. Mine looks like this:

```  
/var/log/syslog
{
        rotate 7
        daily
        missingok
        notifempty
        delaycompress
        compress
        postrotate
                reload rsyslog >/dev/null 2>&1 || true
        endscript
}

/var/log/mail.info
/var/log/mail.warn
/var/log/mail.err
/var/log/mail.log
/var/log/daemon.log
/var/log/kern.log
/var/log/auth.log
/var/log/user.log
/var/log/lpr.log
/var/log/cron.log
/var/log/debug
/var/log/messages

{
        rotate 4
        weekly
        missingok
        notifempty
        compress
        delaycompress
        sharedscripts
        postrotate
                reload rsyslog >/dev/null 2>&1 || true
        endscript
}
``` 

The file starts with defining the instructions for rotating the `/var/log/syslog` file, and the instructions are contained within the curly braces that follow. Here's what they mean:

- `rotate 7`: Keep logs from the last 7 rotations. Then start deleting them.
- `daily`: Rotate the log daily. Along with `rotate 7`, this would mean that logs would be kept for the last 7 days. Other options are `weekly`, `monthly`, `yearly`. There is also a `size` parameter which will rotate log files if its size increases beyond a specified limit. For example, `size 10k`, `size 10M`, `size 10G`, etc. If nothing is specified, logs will be rotated whenever `logrotate` runs. You can even run `logrotate` in a `cron` to use it at more specific time intervals.
- `missingok`: It's okay if the log file is missing. Don't Panic.
- `notifempty`: Don't rotate if the log file is empty.
- `delaycompress`: If compression is on, delay compression until next rotation. This allows at least one rotated but uncompressed file to be present. Useful if you want yesterday's logs to stay uncompressed for troubleshooting. Also useful if some program might still write to the old file until it is restarted/reloaded, like Apache.
- `compress`: Compression is on. Use `nocompress` to turn it off.
- `postrotate/endscript`: Run the script within this section after rotation. Useful for doing cleanup stuff. There is also a `prerotate/endscript` for doing things before rotation begins.

In the configuration above, can you figure out what the next section does, for all those files mentioned? The only additional parameter in the second section is `sharedscripts`, which tells `logrotate` to not run the section within `postrotate/endscript` until all log rotation is complete. It prevents the script from being executed for every log rotated, and just runs once at the end.

## Something New
I'm using the following configuration for dealing with Nginx access and error logs on my system.

```
/var/log/nginx/access.log
/var/log/nginx/error.log  {
        size 1
        missingok
        notifempty
        create 544 www-data adm
        rotate 30
        compress
        delaycompress
        dateext
        dateformat -%Y-%m-%d-%s
        sharedscripts
        extension .log
        postrotate
                service nginx reload
        endscript
}
```

The above script can be run using:

```
logrotate -vs state-file /tmp/logrotate
```

Running the command for the first time gives this output:

```
reading config file /tmp/logrotate
extension is now .log

Handling 1 logs

rotating pattern: /var/log/nginx/access.log
/var/log/nginx/error.log   1 bytes (30 rotations)
empty log files are not rotated, old logs are removed
considering log /var/log/nginx/access.log
  log needs rotating
considering log /var/log/nginx/error.log
  log does not need rotating
rotating log /var/log/nginx/access.log, log->rotateCount is 30
Converted ' -%Y-%m-%d-%s' -> '-%Y-%m-%d-%s'
dateext suffix '-2017-01-27-1485508250'
glob pattern '-[0-9][0-9][0-9][0-9]-[0-9][0-9]-[0-9][0-9]-[0-9][0-9][0-9][0-9][0-9][0-9][0-9][0-9][0-9][0-9]'
glob finding logs to compress failed
glob finding old rotated logs failed
renaming /var/log/nginx/access.log to /var/log/nginx/access-2017-01-27-1485508250.log
creating new /var/log/nginx/access.log mode = 0544 uid = 33 gid = 4
running postrotate script
* Reloading nginx configuration nginx
```

... and a second time:

```
reading config file /tmp/logrotate
extension is now .log

Handling 1 logs

rotating pattern: /var/log/nginx/access.log
/var/log/nginx/error.log   1 bytes (30 rotations)
empty log files are not rotated, old logs are removed
considering log /var/log/nginx/access.log
  log needs rotating
considering log /var/log/nginx/error.log
  log does not need rotating
rotating log /var/log/nginx/access.log, log->rotateCount is 30
Converted ' -%Y-%m-%d-%s' -> '-%Y-%m-%d-%s'
dateext suffix '-2017-01-27-1485508280'
glob pattern '-[0-9][0-9][0-9][0-9]-[0-9][0-9]-[0-9][0-9]-[0-9][0-9][0-9][0-9][0-9][0-9][0-9][0-9][0-9][0-9]'
compressing log with: /bin/gzip
renaming /var/log/nginx/access.log to /var/log/nginx/access-2017-01-27-1485508280.log
creating new /var/log/nginx/access.log mode = 0544 uid = 33 gid = 4
running postrotate script
* Reloading nginx configuration nginx
```

... and a third time:

```
reading config file /tmp/logrotate
extension is now .log

Handling 1 logs

rotating pattern: /var/log/nginx/access.log
/var/log/nginx/error.log   1 bytes (30 rotations)
empty log files are not rotated, old logs are removed
considering log /var/log/nginx/access.log
  log needs rotating
considering log /var/log/nginx/error.log
  log does not need rotating
rotating log /var/log/nginx/access.log, log->rotateCount is 30
Converted ' -%Y-%m-%d-%s' -> '-%Y-%m-%d-%s'
dateext suffix '-2017-01-27-1485508316'
glob pattern '-[0-9][0-9][0-9][0-9]-[0-9][0-9]-[0-9][0-9]-[0-9][0-9][0-9][0-9][0-9][0-9][0-9][0-9][0-9][0-9]'
compressing log with: /bin/gzip
renaming /var/log/nginx/access.log to /var/log/nginx/access-2017-01-27-1485508316.log
creating new /var/log/nginx/access.log mode = 0544 uid = 33 gid = 4
running postrotate script
* Reloading nginx configuration nginx
```

The contents of the state file look like this:

```
logrotate state -- version 2
"/var/log/nginx/error.log" 2017-1-27-9:0:0
"/var/log/nginx/access.log" 2017-1-27-9:11:56
```

## Resources:
<a href="http://www.linuxcommand.org/man_pages/logrotate8.html" target="_blank">http://www.linuxcommand.org/man_pages/logrotate8.html</a>