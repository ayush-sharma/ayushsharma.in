---
layout: post
title:  "Getting Started with time-series data using InfluxDB"
number: 45
date:   2017-08-21 0:00
categories: databases
---
Before we get started with InfluxDB, it is important to understand something about time series databases.

## Introduction
A time series database is one which is optimised to handle time series data, which is data indexed by time. A time series is a sequence of data points taken over equally spaced points in time. This series of a particular collection of values with respect to time is sometimes also called a profile, a trace, or a curve. Time series database are specially designed for the high transaction volume required for keeping time series data, which may not be possible with traditional relational databases. Two examples of time series databases are Graphite, written in Python, and InfluxDB, written in Go.

## Relational databases vs time-series databases
Let’s look at a small example.

Traditionally, relational database systems have been used for OLTP (online transaction processing) operations. A banking database might consist rows containing the current balances of two accounts. Transferring money from one to the other likely involves updating the current balance in two different rows, one for the sender and one for the receiver, respectively. These two rows may be spread out over the database table. Also, both operations are critical. Which means that an RDBMS needs to be able to allow updating rows randomly over its dataset and be ACID compliant to ensure data integrity and consistency.

What about collecting CPU metrics from hundreds of servers in real-time? To store this in a database, I would only ever insert rows, since data is real-time, and there is no need to update older entries, since I really don’t need to update the CPU percentage of a server 2 days ago. And it would still be okay to lose a data point or two, since this data is not really critical or transactional in nature. I also need to be able to make inserts very quickly, since I will be getting a lot of data of very high granularity from a variety of systems. Additionally, if I need to diagnose a problem, I need to know what happened in the last hour or so, for which I need data with very high granularity, say every second. But for running trends for any metric over the last one year, lower resolution metrics will do. Another aspect to consider is that in a scenario such as alerting or reporting, it would be okay for me to miss some of the data, but I need to be able to write most of the data even during extremely high load. For these reasons, time-series databases might prioritise new data over old data, and provide mechanisms for expiring data. They also need to favour create and read over update and delete, since in use cases of storing real-time data, the chances of going back and modifying old data might be very small.

Because of these differences in the use cases, and the increasing requirements to log every metric from every system possible for alerting and reporting, time-series databases were created to get over the limitations of traditional relational database systems.

InfluxDB is a time-series database. Whether you need it or not will depend entirely on your use case.

If InfluxDB is for you, keep reading to get started.

## Installing InfluxDB
I won’t go over installing InfluxDB in detail. The [installation docs located here](https://docs.influxdata.com/influxdb/v0.9/introduction/installation/) are pretty comprehensive. On Mac OS X Sierra, which is where I’ll be testing this, it is as simple as running this:

```bash
brew update
brew install influxdb
```

To start InfluxDB once installed, just run:

```bash
influxd -config /usr/local/etc/influxdb.conf
```

## Creating a Database and Running Some Queries
If you ran the previous command, then InfluxDB must have started in that command-line window. Open a separate command-line window, and run the following:

```bash
influx
```

This will drop you down to the InfluxDB prompt. Once there, run the following commands:

```sql
CREATE DATABASE testing
use testing
INSERT cpu_metrics,server=host1,region=us-east-1 value=0.67
INSERT cpu_metrics,server=host1,region=us-east-1 value=0.62
INSERT cpu_metrics,server=host1,region=us-east-1 value=0.63
INSERT cpu_metrics,server=host1,region=us-east-1 value=0.65
INSERT cpu_metrics,server=host1,region=us-east-1 value=0.55
INSERT cpu_metrics,server=host2,region=us-west-2 value=0.67
INSERT cpu_metrics,server=host2,region=us-west-2 value=0.62
INSERT cpu_metrics,server=host2,region=us-west-2 value=0.63
INSERT cpu_metrics,server=host2,region=us-west-2 value=0.65
INSERT cpu_metrics,server=host2,region=us-west-2 value=0.55
```

Here’s what we just did:
1. The first 2 lines are pretty basic, and should be familiar from SQL. We’re creating a new database, and then `use`-ing it.
2. In the next few lines, we’re inserting new data into our database. These insert statements should not have given you any output, which meant they ran fine. In InfluxDB, no news is good news. The format of the insert statement is as follows:

```sql
INSERT <measurement>,<tag_key>=<tag_value> <field_key>=<field_value>
```

`Measurement` is essentially the table name, and will stay the same across multiple inserts. The `tags` and `fields`, which are both key-value pairs, are essentially additional data that you would like to associate with that data point. The keys are essentially column names and values are column values. `Tag` keys/values are specified immediately after the measurement, and `field` key/values are specified after a space. Multiple `tag` and `field` key/value pairs can be separated with a comma. Both `tags` and `fields` are used to select and filter data, for example in `where` clauses, but with one key difference: `tags` are indexed, but `fields` are not. This is important, and will significantly impact the run times of your queries and the storage requirements of your database. Please review your queries before your finalise your database schema. InfluxDB is schema-less, which means you can add new `tags` and `fields` to new data points on the fly, but what should be a `tag` and what should be a `field` is an important consideration.

Let’s select the data we have just inserted by running some queries. I’m mentioning the query and the output for 3 queries that we should try.

```sql
SELECT * FROM "cpu_metrics"
name: cpu_metrics
time                region    server value
----                ------    ------ -----
1503242139992345061 us-east-1 host1  0.67
1503242140000173781 us-east-1 host1  0.62
1503242140001901034 us-east-1 host1  0.63
1503242140003284167 us-east-1 host1  0.65
1503242140004340394 us-east-1 host1  0.55
1503242140005448769 us-west-2 host2  0.67
1503242140006378791 us-west-2 host2  0.62
1503242140007501112 us-west-2 host2  0.63
1503242140008516967 us-west-2 host2  0.65
1503242140876640123 us-west-2 host2  0.55
```

```sql
SELECT * FROM "cpu_metrics" WHERE region = 'us-east-1'
name: cpu_metrics
time                region    server value
----                ------    ------ -----
1503242139992345061 us-east-1 host1  0.67
1503242140000173781 us-east-1 host1  0.62
1503242140001901034 us-east-1 host1  0.63
1503242140003284167 us-east-1 host1  0.65
1503242140004340394 us-east-1 host1  0.55
```

```sql
SELECT * FROM "cpu_metrics" WHERE "region" = 'us-west-2'
name: cpu_metrics
time                region    server value
----                ------    ------ -----
1503242140005448769 us-west-2 host2  0.67
1503242140006378791 us-west-2 host2  0.62
1503242140007501112 us-west-2 host2  0.63
1503242140008516967 us-west-2 host2  0.65
1503242140876640123 us-west-2 host2  0.55
```

So we were able to select the values we inserted. We were able to do an unqualified select for getting all values, and then using our `tag`s to select data. Please remember to specify `tag` values in single-quotes only, otherwise they might fail.

The `time` column you see is present in all tables, and is automatically inserted if you don’t specify it. It will be equal to the current UNIX timestamp in nanoseconds when you run the insert command. Timestamps in InfluxDB are always UTC.

## Resources
- [Getting Started InfluxDB documentation](https://docs.influxdata.com/influxdb/v0.9/introduction/getting_started/).
- [Comparison of time-series databases](https://blog.outlyer.com/top10-open-source-time-series-databases).