---
layout: post
title:  "Setting up Collectd and InfluxDB on Mac OS X"
number: 46
date:   2017-08-21 1:00
categories: monitoring
---
We've covered the basics of [Collectd]({% post_url 2017-08-17-getting-started-with-server-metrics-collecting-with-collectd %}) and [InfluxDB]({% post_url 2017-08-21-getting-started-with-time-series-data-using-influxdb %}), and now it's time to get started and connect the two. The goal of this exercise is to have Collectd collect some metrics from my local Mac OS X Sierra, and push them to a local InfluxDB.

## Setting up InfluxDB
You can look at the [InfluxDB]({% post_url 2017-08-21-getting-started-with-time-series-data-using-influxdb %}) post and find the installation instructions there. Once that's done, create the database `collectd_metrics` for storing metrics by running the following:

```bash
curl http://localhost:8086/query --data-urlencode "q=CREATE DATABASE collectd_metrics"
```

The above command uses the HTTP API built into InfluxDB to create a database called `collectd_metrics` which we will be using to store our metrics. Then, edit `/usr/local/etc/influxdb.conf` and add the following configuration:

```bash
[[collectd]]
enabled = true
bind-address = ":25826"
database = "collectd_metrics"
retention-policy = ""
batch-size = 5000
batch-pending = 10
batch-timeout = "10s"
read-buffer = 0
typesdb = "/usr/local/Cellar/collectd/5.7.2/share/collectd/types.db"
```

The above will tell InfluxDB to expect data from Collectd and use the same `types` files as Collectd so it can understand the language Collectd speaks. We're also specifying the database into which InfluxDB should put all data from Collectd, which we created above.

Save the file, and restart InfluxDB using `brew services restart influxdb`. You can check the log file for errors, which on Mac is located at `/usr/local/var/log/influxdb.log`.

## Setting up Collectd
Check out the [Collectd]({% post_url 2017-08-17-getting-started-with-server-metrics-collecting-with-collectd %}) post to find installation instructions. On a Mac it's as simple as running `brew install collectd`. Once installed, update `/usr/local/etc/collectd.conf` with the following configuration:

```bash
Hostname "localhost"
FQDNLookup false
BaseDir "/usr/local/var/lib/collectd"
PIDFile "/usr/local/var/run/collectd.pid"
TypesDB "/usr/local/Cellar/collectd/5.7.2/share/collectd/types.db"

AutoLoadPlugin true

Interval 1

MaxReadInterval 86400
Timeout 2
ReadThreads 5
WriteThreads 5

<Plugin cpu>
 ReportByCpu true
 ReportByState true
 ValuesPercentage true
</Plugin>

<Plugin load>
 ReportRelative true
</Plugin>

<Plugin memory>
 ValuesAbsolute true
 ValuesPercentage false
</Plugin>

<Plugin "network">
  Server "localhost" "25826"
</Plugin>
```

Note that we're configuring the same `types` database as InfluxDB. This file contains the types specification for Collectd and keeping it the same across Collectd and InfluxDB configurations will ensure they speak the same language.

Once the configuration file is updated, restart Collectd using `sudo brew services restart collectd`. Collectd must be run as a root user in order for the CPU plugin to be loaded correctly. You can check the log file for any errors, which on Mac is located at `/usr/local/var/log/collectd.log`.

## Checking it out
If everything went correctly and there were no errors, both your services should be up and running with the new configuration, and your Collectd data should now be in InfluxDB. Let's see if we can find it.

Open up the InfluxDB CLI console by hitting `influx` on the command-line. Enter the following commands...

```bash
> use collectd_metrics
Using database collectd_metrics
> show measurements
name: measurements
name
----
cpu_value
load_longterm
load_midterm
load_shortterm
memory_value
```

... and you should see a list of metrics from the plugins we've enabled in Collectd. Now it's just a matter of using the InfluxDB query language to explore our dataset. Here are a few samlpe queries.

```bash
> SELECT * FROM "cpu_value" ORDER BY time DESC LIMIT 10
name: cpu_value
time host instance type type_instance value
---- ---- -------- ---- ------------- -----
1503312861108529000 localhost 1 percent nice 0
1503312861108440000 localhost 1 percent system 0.9900990099009903
1503312861108242000 localhost 1 percent user 2.9702970297029703
1503312861108008000 localhost 0 percent idle 88
1503312861107800000 localhost 0 percent nice 0
1503312861107677000 localhost 0 percent system 4.000000000000001
1503312861107634000 localhost 0 percent user 8.000000000000002
1503312860109402000 localhost 3 percent idle 89
1503312860109390000 localhost 3 percent nice 0
1503312860109359000 localhost 3 percent system 3
```

```bash
> SELECT * FROM load_shortterm LIMIT 5
name: load_shortterm
time host type type_instance value
---- ---- ---- ------------- -----
1503302788283096000 localhost load relative 0.5023193359375
1503302789345247000 localhost load relative 0.5023193359375
1503302790343697000 localhost load relative 0.5023193359375
1503302791342099000 localhost load relative 0.5023193359375
1503302792341093000 localhost load relative 0.4820556640625
```

```bash
> SELECT * FROM "memory_value" LIMIT 5
name: memory_value
time host type type_instance value
---- ---- ---- ------------- -----
1503302788283027000 localhost memory active 2541314048
1503302788283027000 localhost memory wired 1665589248
1503302788283027000 localhost memory inactive 1730576384
1503302788283027000 localhost memory free 226271232
1503302789345226000 localhost memory inactive 1730584576
```

```bash
> SELECT LAST(value) FROM "memory_value" WHERE type_instance = 'free'
name: memory_value
time last
---- ----
1503304076339932000 330047488
```

We'll wrap this up here. Check out the resources for more information.

## Resources
- [InfluxDB query syntax](https://docs.influxdata.com/influxdb/v0.9/query_language/data_exploration/).
- [InfluxDB frequently encountered issues](https://docs.influxdata.com/influxdb/v0.9/troubleshooting/frequently_encountered_issues/).
- [Collectd FAQ](https://collectd.org/faq.shtml).