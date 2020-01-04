---
layout: post
title:  "Connecting VPCs in 2 AWS Regions (Site-to-site VPN)"
number: 29
date:   2017-02-14 0:00
categories: networking
---
AWS provides a feature called VPC Peering that allows you to connect two VPCs in the same region. But what if you want to connect VPCs in two different AWS regions?  What if you want a machine to be able to access another machine in another region using its private IP address? This guide will help you set that up.

## 1. Setting Up The VPCs
Look at the following diagram.

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}vpn-architecture-diagram.jpg" width="700" height="385" alt="VPN architecture.">

There are 4 instances in the diagram. Two VPNs, one each in the public subnet of the VPC in each region, and another 2 test machines, in the private subnet of the VPC in each region. Our goal is to be able to connect the test machines to each other. You should be able to ping one of those machines from the other using the private IP address.

First, let's create 2 VPCs, one in Virginia and one in Oregon, with the following configuration.

<table class="mytable">
    <tr>
        <td>Component</td>
        <td>ayush-virginia-vpc</td>
        <td>ayush-oregon-vpc</td>
    </tr>
    <tr>
        <td>CIDR</td>
        <td>10.0.0.0/16</td>
        <td>172.16.0.0/16</td>
    </tr>
    <tr>
        <td>Public Subnet</td>
        <td>10.0.0.0/24</td>
        <td>172.16.0.0/24</td>
    </tr>
    <tr>
        <td>Private Subnet</td>
        <td>10.0.1.0/24</td>
        <td>172.16.1.0/24</td>
    </tr>
</table>

## 2. Setting Up The VPNs

### 2a. Launching The Machines

Now we'll create 2 Ubuntu instances, one in Virginia and one in Oregon, in the newly created VPCs that will act as our VPNs.
Note that:

- These machines must be in the public subnet of their VPCs.
- Make sure their security groups allow incoming and outgoing traffic from all ports. This is not very secure, but will make our configuration easier. You can tighten ports later. Especially open UDP 500 and UDP 4500.
- Disable "Source/Destination check" on both machines.
- Teardown AppArmor on both machines using `/etc/init.d/apparmor teardown`.
- Enable IP forwarding on both VPNs by running `sysctl -w net.ipv4.ip_forward=1`. This tiny step is very important, and will save you a lot of headache.

Once the machines have been launched, assign them an Elastic IP address, and note their public and private IP addresses. Like so:

<table class="mytable">
    <tr>
        <td>Component</td>
        <td>ayush-virginia-vpc</td>
        <td>ayush-oregon-vpc</td>
    </tr>
    <tr>
        <td>VPN Instance Name</td>
        <td>ayush-virginia-vpn</td>
        <td>ayush-oregon-vpn</td>
    </tr>
    <tr>
        <td>VPN Private IP</td>
        <td>10.0.0.207</td>
        <td>172.16.0.29</td>
    </tr>
    <tr>
        <td>VPN Instance EIP</td>
        <td>54.197.XXX.XXX</td>
        <td>35.162.XXX.XXX</td>
    </tr>
</table>

### 2b. Installing Strongswan
Use the following:

```
apt-get -y install strongswan
```

### 2c. Configuring Strongswan On Both VPNs:
Let's add some logging to `/etc/strongswan.d/charon-logging.conf`:

```
charon {

    filelog {
        /var/log/strongswan.log {
            # add a timestamp prefix
            time_format = %b %e %T
            # prepend connection name, simplifies grepping
            ike_name = yes
            # overwrite existing files
            append = yes
            # increase default loglevel for all daemon subsystems
            default = 2
            # flush each line to disk
            flush_line = yes
        }
        stderr {
            # more detailed loglevel for a specific subsystem, overriding the
            # default loglevel.
            ike = 2
            knl = 3
        }
    }
}
``` 

Let's configure defaults on both VPNs in `/etc/ipsec.conf`:

```
config setup
  charondebug="all"
  uniqueids=yes
  strictcrlpolicy=no

conn %default
  ikelifetime=60m
  keylife=20m
  rekeymargin=3m
  keyingtries=1
  keyexchange=ikev2

include /etc/ipsec.d/*.conf
```

### 2d. Configuring Virginia VPN
Add the following to  `/etc/ipsec.d/vpc-virginia.conf`:

```
conn vpc-virginia
        type=tunnel
        authby=secret
        left=10.0.0.207
        leftid=54.197.XXX.XXX
        leftsubnet=10.0.0.0/16
        right=35.162.XXX.XXX
        rightsubnet=172.16.0.0/16
        leftauth=psk
        rightauth=psk
        esp=aes256-sha1-modp1536
        ike=aes256-sha1-modp1536
        auto=start
``` 

Add the following to `/etc/ipsec.secrets`:

```
10.0.0.207 35.162.XXX.XXX : PSK "THIS__IS__SPARTA!!!"
54.197.XXX.XXX 35.162.XXX.XXX : PSK "THIS__IS__SPARTA!!!"
```

### 2e. Configuring Oregon VPN
Add the following to `/etc/ipsec.d/vpc-oregon.conf`:

```
conn vpc-oregon
        type=tunnel
        authby=secret
        left=172.16.0.29
        leftid=35.162.XXX.XXX
        leftsubnet=172.16.0.0/16
        right=54.197.XXX.XXX
        rightsubnet=10.0.0.0/16
        leftauth=psk
        rightauth=psk
        esp=aes256-sha1-modp1536
        ike=aes256-sha1-modp1536
        auto=start
```

Add the following to `/etc/ipsec.secrets`:

```
172.16.0.29 54.197.XXX.XXX : PSK "THIS__IS__SPARTA!!!"
35.162.XXX.XXX 54.197.XXX.XXX : PSK "THIS__IS__SPARTA!!!"
```

### 3. Restart Services
On both VPN machines, do:

```
service strongswan restart
ipsec stop
ipsec start
``` 

### 4. Checking The Tunnel
Do `ipsec status` on both machines and you should see the following.

On the Virginia VPN:

```
vpc-virginia[1]: ESTABLISHED 43 minutes ago, 10.0.0.207[54.197.XXX.XXX]...35.162.XXX.XXX[35.162.XXX.XXX]
vpc-virginia{4}:  INSTALLED, TUNNEL, reqid 1, ESP in UDP SPIs: cdb651d5_i c70b50c8_o
vpc-virginia{4}:   10.0.0.0/16 === 172.16.0.0/16
```

On the Oregon VPN:

```
vpc-oregon[2]: ESTABLISHED 44 minutes ago, 172.16.0.29[35.162.XXX.XXX]...54.197.XXX.XXX[54.197.XXX.XXX]
vpc-oregon{5}:  INSTALLED, TUNNEL, reqid 2, ESP in UDP SPIs: c70b50c8_i cdb651d5_o
vpc-oregon{5}:   172.16.0.0/16 === 10.0.0.0/16
```

## 5. Routing table
This is the main part. In the Virginia VPC, we need to tell it to route the requests to `172.16.0.0/16` through the Virginia VPN, and the reverse for Oregon. Your routing table configuration should look like this:

For Virginia:

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}vpn-routing-table-for-virginia.jpg" width="449" height="153" alt="VPN routing table for Virginia.">

For Oregon:

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}vpn-routing-table-for-oregon.jpg" width="448" height="154" alt="VPN routing table for Oregon.">

## 6. Testing
Launch two new machines, "Virginia Test" and "Oregon Test", as in the diagram. Make sure you allow traffic from all ports and launch them in the VPCs you created. Like so:

<table class="mytable">
    <tr>
        <td>Component</td>
        <td>ayush-virginia-vpc</td>
        <td>ayush-oregon-vpc</td>
    </tr>
    <tr>
        <td>Private Instance Name</td>
        <td>ayush-virginia-private-test</td>
        <td>ayush-oregon-private-test</td>
    </tr>
    <tr>
        <td>Private Instance IP</td>
        <td>10.0.1.105</td>
        <td>172.16.1.128</td>
    </tr>
</table>

If the configuration is correct, you should be able to ping each machine from the others using their private IP addresses.

From Virginia:

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}testing-vpn-from-virginia-ping-1.jpg" width="405" height="87" alt="Testing VPN from Virginia ping 1.">

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}testing-vpn-from-virginia-ping-2.jpg" width="422" height="99" alt="Testing VPN from Virginia ping 2.">

From Oregon:

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}testing-vpn-from-oregon-ping-1.jpg" width="403" height="83" alt="Testing VPN from Oregon ping 1.">

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}testing-vpn-from-oregon-ping-2.jpg" width="402" height="85" alt="Testing VPN from Oregon ping 2.">



And that's it!

Good luck ;)
