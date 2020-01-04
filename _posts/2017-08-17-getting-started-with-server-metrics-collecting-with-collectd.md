---
layout: post
title:  "Getting started with server metrics collection with Collectd"
number: 44
date:   2017-08-17 0:00
categories: monitoring
---
Collectd is a daemon written in C which allows the collection of a variety of metrics from the OS, log files, etc., and provides the capability of writing these metrics over the network. It's really useful for gathering metrics for making informed decisions when it comes to finding application bottlenecks and planning capacity. Since Collectd runs in memory and has a very small footprint, it allows the gathering of very high resolution metrics, as high as 10 seconds.

Collectd has support for both unicast and multicast setups. Collectd can be used in client mode, where it pushes data to another Collectd server, or in server mode, where it aggregates data from multiple clients. It also has quite a lot of plugins for collecting metrics from the operating system, web servers, application log files, etc.

The standard Collectd configuration file is `collectd.conf`, which controls how Collectd behaves and which metrics it collects.

To understand this tool a little better, let's get hands-on and try something out.

## Install Collectd
I won't spend a lot of time on installation instructions. You can find those for your distro over (here)[https://collectd.org/download.shtml].
Once you've installed it, check the version by running `collectd -h`. I'll be using `collectd 5.5.2`.

Edit `/etc/collectd/collectd.conf`, and replace everything in it with the following:

```bash
Hostname "this_host"
Interval 60

FQDNLookup false

LoadPlugin cpu
LoadPlugin csv

<Plugin "cpu">
  ReportByState true
  ReportByCpu true
  ValuesPercentage true
</Plugin>

<Plugin "csv">
  DataDir "/var/lib/collectd/csv"
  StoreRates true
</Plugin>
```

Then restart Collectd using `service collectd restart`.

Once you restart, there should be a new folder within `/var/lib/collectd/csv` called `this_host`. Within this folder, there should be a folder for very CPU core you have. For example, I have multiple folders with the names `cpu-1`, `cpu-2`, etc., since I have a multi-core system.

With this folder, there will be multiple files like the following:

```
percent-idle-2017-08-17
percent-interrupt-2017-08-17
percent-nice-2017-08-17
percent-softirq-2017-08-17
percent-steal-2017-08-17
percent-system-2017-08-17
percent-user-2017-08-17
percent-wait-2017-08-17
```

As you can see, each file is a CSV file containing percentage values of CPU idle %, steal %, etc. These files will contain values like these:

```
epoch,value
1502967403.686,99.732888
1502967463.686,99.782972
1502967523.687,99.616027
1502967583.687,99.833083
```

The first line contains the headers, and the next lines contain the UNIX timestamp of the data point, and the value of the data point, depending on which file you're seeing.

You can use the simple configuration describe above to collect a variety of metrics from your system. From system CPU, memory, network and battery metrics, to Nginx, Apache, MySQL metrics, and even custom metrics. Once you have these metrics, there are a number of options available, from writing them to files in various formats, to sending them over the network somewhere else for aggregation, processing, and reporting. You can view a list of plugins (here)[https://collectd.org/wiki/index.php/Table_of_Plugins].

Collectd seems really great as a lightweight metrics collection tool, and in combination with something like a time-series database and a reporting tool, it really has a lot of potential for setting up a scalable reporting infrastructure. I'll be looking at InfluxDB and Grafana next, to see how they pair with Collectd to remove some monitoring headaches and get some fancy graphs going.

Post questions in the comments below.

## Resources
- [Homepage](https://collectd.org/).
- [Plugins](https://collectd.org/wiki/index.php/Table_of_Plugins).
- [Project GitHub page](https://github.com/collectd/collectd).