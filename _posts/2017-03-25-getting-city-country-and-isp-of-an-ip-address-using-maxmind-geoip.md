---
layout: post
title:  "Getting City, Country and ISP of an IP address using Maxmind GeoIP"
number: 36
date:   2017-03-25 0:00
categories: networking
---


Getting the location of an IP address is something very basic that we need to do for a variety of reasons. You may need it for your application, or for diagnosing an issue on your server. Tracing the origin of an IP address can be critical, and currently the industry is using Maxmind.

Log in to your Maxmind account and go to [download databases](https://www.maxmind.com/en/download_files). We're going to be working with Legacy APIs today, so click on the GZIP download link for legacy APIs for the following databases:

- GeoIP 106: GeoIP Legacy Country
- GeoIP 121: GeoIP Legacy ISP
- GeoIP 133: GeoIP Legacy City with DMA/Area Codes

Untar the files you download, and you will end up with:

- GeoIP-106_20170124.dat
- GeoIPISP.dat
- GeoIPCity.dat

## Installing the CLI

Fire up your Ubuntu 16.04, and run the following to install the `geoiplookup` binary:

`apt-get update; apt-get install geoip-bin`

## Getting the Country
Run `geoiplookup -f GeoIP-106_20170124.dat 139.59.6.156` and you get:

```
GeoIP Country Edition: IN, India
``` 

## Getting the ISP
Run `geoiplookup -f GeoIPISP.dat 139.59.6.156` and you get:

```
GeoIP ISP Edition: Digital Ocean
```

## Getting the City and State
Run `geoiplookup -f GeoIPCity.dat 139.59.6.156` and you get:

```
GeoIP City Edition, Rev 1: IN, 19, Karnataka, Bangalore, 560100, 12.983300, 77.583298, 0, 0
```

There are other databases that you can try. You might also want to try GeoIP2, although i haven't checked that out yet. Post in the comments if you find GeoIP2 is any better.