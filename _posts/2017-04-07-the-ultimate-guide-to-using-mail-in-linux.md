---
layout: post
title:  "The Ultimate Guide to Using Mail in Linux"
number: 39
date:   2017-04-07 0:00
categories: networking
---
Sending email from the command-line should not be complicated, so I've compiled a list of frequently used commands. I'm using `Mutt` instead of `mailutils`, since the latest version of the `mail` command `mail (GNU Mailutils) 2.99.98` does not have support for the CC and BCC parameters directly.

We're going to install the `Mutt` utility on Ubuntu 16.04. Do this:

```bash
apt-get update
apt-get install mutt
```

Run `mutt -v` to check the version after installation. We're working with `Mutt 1.5.21 (2010-09-15)`.

## Sending Emails

This is the basic email command.

```bash
mutt -s "This is my subject." emailone@example.com
```

This will open an interactive panel so you can compose and configure your message. The shortcuts to navigate this interface are at the top, and the prompts are at the bottom. You can hit `y` to send your message when you're done.

### Parameters
The `mutt` command has some helpful parameters.

- `-s`: Subject of the message.
- `-c`: CC email address.
- `-b`: BCC email address.
- `-a`: File to attach.

### Examples
Sending email with message body.

```bash
mutt -s "This is my subject." emailone@example.com <<< "This is my body."
mutt -s "This is my subject." emailone@example.com,emailtwo@example.com <<< "This is my body."
```

```bash
echo "This is my body." | mutt -s "This is my subject." emailone@example.com
```

```bash
cat /tmp/message_body | mutt -s "This is my subject." emailone@example.com
```

```bash
mutt -s "This is my subject." emailone@example.com < /tmp/message_body
```

Sending email without message body.

```bash
mutt -s "This is my subject." emailone@example.com < /dev/null
```

Sending email with multi-line message body.

```bash
mutt -s "This is my subject." emailone@example.com <<EOF
This
is
my
body
.
EOF
```

Sending CC and BCC. Use the `-c` and `-b` flags to specify carbon-copy and blind-carbon-copy recipients.

```bash
mutt -s "This is my subject." emailone@example.com -c emailcc@example.com -b emailbcc@example.com <<< "This is my body."
```

For sending attachments, just use `-a` with any of the above commands. Like so:

```bash
mutt -s "This is my subject." emailone@example.com -a /tmp/message_body < /dev/null
mutt -s "This is my subject." emailone@example.com -a /tmp/file_1.txt /tmp/file_2.txt < /dev/null
```

### Some Use Cases
Email current disk usage report.

```bash
df -h | mutt -s "Disk Usage Report" emailone@example.com
```

Email current disk and memory usage report.

```bash
df -h > /tmp/report.log
free -m >> /tmp/report.log
mutt -s "Disk and Memory Usage Report" emailone@example.com < /tmp/report.log
mutt -s "Disk and Memory Usage Report (Attachment)" emailone@example.com -a /tmp/report.log < /dev/null
```