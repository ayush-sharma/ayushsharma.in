---
layout: post
title:  "Using Route 53 as a Load Balancer"
number: 42
date:   2017-08-12 0:00
categories: networking
---
Given our very high costs of using AWS ELB as a load balancer, we decided to try to investigate using Route 53 instead. Yes, it’s not really a load balancer, but it seemed to provide an easy to use solution for providing load balancing while we spent the time to build our own using HAProxy. The AWS ELB does 3 things: it selects an IP address using round-robin based on a pool of IP addresses, it ensures only healthy IP addresses are selected, and it provides graphs and dashboards for monitoring and alerting. We wanted to find out if 1 and 2 could be done in Route 53. Given that its a global, fully-managed service and is not that expensive, the prospect seemed very promising. But Route 53 has limitations that makes using it as a load balancer not very elegant. The POC was not successful, but I’m putting my thoughts down here for future reference.

Route 53 has the ability to create multiple records with the same name and type, and associate them all with health checks. You can’t create multiple A records with the same name, but you can if you make them weighted. Creating these multiple records with a weight of 1 will get over the limitation, and ensure all the records are returned an equal number of times. So we can have 100 entries with same names and different values of the IP addresses of the machines. When the domain is requested, one of the 100 IPs is selected and returned. These IPs can be associated with health checks so that only healthy ones are returned.

Of course, we need more than 100. So the option can be to add more than 1 IP to a record. Up to 100 values can be added to a single record, meaning we can potentially have 100 X 100 IP addresses, which is sufficient for our use case for now.

In order to distribute IP addresses among the 100 available records, we converted the IP addresses into an integer and took a modulus with 100 to get a IP “bucket”. A bucket would basically determine which record the IP would go into. By adding this bucket name to the set identifier while creating the record, we can pull that record with the set identifier when updating the record with more IP addresses. The obvious downside with this approach is that a bucket could get exhausted if too many machines had IPs within a single bucket, but the solution for that would be to distribute IPs within the buckets evenly instead of based on a modulus of the IP address.

Health checks were harder with this approach, since health checks are for entire records and not for individual IPs within that record. If I’m adding 3 different IPs to a record, ideally I want only the healthy IPs returned, which means health checks should have a way of knowing which of those 3 IPs is healthy. But that’s really not how Route 53 health checks work. You can only add health checks for entire records, which means failure of that health check invalidates multiple IPs. I confirmed this by posting the question on the AWS forums and got the same response.

All in all, using Route 53 as a load balancer is a bad idea, which not entirely unfair. A proper load balancer can handle individual health checks and respond to health changes much faster. I’m moving to investigating HAProxy to get this done, but this was a nice failed experiment.

Here is the script I used to add/remove the IPs. It’s also available on [github](https://github.com/ayush-sharma/infra_helpers/blob/master/aws/aws_route53_register_deregister_instance.py).

```python
import boto3
import requests
import argparse
import socket
import struct
import sys
from time import sleep


def add_entry(this_ip: str, hosted_zone_id: str, record_bucket: int, record_set_name: str, record_type: str,              record_ttl: str, ip_list: list):
    """ Add entry for this machine in record set. """        response = ''        route53_client = boto3.client('route53')
    
    counter = 1    while counter <= 25:
        
        try:
            response = route53_client.change_resource_record_sets(
                    HostedZoneId=hosted_zone_id,                    ChangeBatch={
                        'Comment': 'string',                        'Changes': [
                            {
                                'Action'           : 'UPSERT',                                'ResourceRecordSet': {
                                    'Name'           : record_set_name,                                    'Type'           : record_type,                                    'TTL'            : record_ttl,                                    'ResourceRecords': ip_list,                                    'Weight'         : 1,                                    'SetIdentifier'  : record_set_name + '_' + str(record_bucket)
                                    # 'HealthCheckId': 'string',                                }
                            },                        ]
                    }
            )
            
            print(this_ip + ' >>> IP insert complete.')
            
            break                except Exception as e:
            
            sleepy_time = counter ** 2            print(this_ip + ' XXX Exception: %s' % str(e.args))
            print(this_ip + ' XXX Iteration %s, sleeping for %s seconds.' % (str(counter), str(sleepy_time)))
            sleep(sleepy_time)
        
        counter += 1        return response


def remove_entry(this_ip: str, hosted_zone_id: str, record_bucket: int, record_set_name: str, record_type: str,                 record_ttl: str, ip_list: list):
    """ Remove entry for this machine in record set. """        response = ''        route53_client = boto3.client('route53')
    
    counter = 1    while counter <= 25:
        
        try:
            response = route53_client.change_resource_record_sets(
                    HostedZoneId=hosted_zone_id,                    ChangeBatch={
                        'Comment': 'string',                        'Changes': [
                            {
                                'Action'           : 'DELETE',                                'ResourceRecordSet': {
                                    'Name'           : record_set_name,                                    'Type'           : record_type,                                    'TTL'            : record_ttl,                                    'ResourceRecords': ip_list,                                    'Weight'         : 1,                                    'SetIdentifier'  : record_set_name + '_' + str(record_bucket)
                                    # 'HealthCheckId': 'string',                                }
                            },                        ]
                    }
            )
            
            print(this_ip + ' >>> IP delete complete.')
            
            break                except Exception as e:
            
            sleepy_time = counter ** 2            print(this_ip + ' XXX Exception: %s' % str(e.args))
            print(this_ip + ' XXX Iteration %s, sleeping for %s seconds.' % (str(counter), str(sleepy_time)))
            sleep(sleepy_time)
        
        counter += 1        return response


def get_ip_bucket(ip):
    """ Convert an IP string to long. """        packed_ip = socket.inet_aton(ip)
    return struct.unpack("!L", packed_ip)[0] % 100

def get_ips_for_bucket(hosted_zone_id, record_name, bucket):
    response = ''        route53_client = boto3.client('route53')
    
    counter = 1    while counter <= 25:
        
        try:
            response = route53_client.list_resource_record_sets(
                    HostedZoneId=hosted_zone_id
            )
            
            break                except Exception as e:
            
            sleepy_time = counter ** 2            print('Exception trying to get list of records for bucket.')
            print('XXX Exception: %s' % str(e.args))
            print('XXX Iteration %s, sleeping for %s seconds.' % (str(counter), str(sleepy_time)))
            sleep(sleepy_time)
        
        counter += 1        response = response['ResourceRecordSets']
    
    ip_list = []
    for response_item in response:
        
        if 'SetIdentifier' in response_item and response_item['SetIdentifier'] == record_name + '_' + str(bucket):
            
            for resource_records in response_item['ResourceRecords']:
                ip_list.append(resource_records['Value'])
    
    return ip_list


def get_value_string_from_ips(ip_list: list):
    ip_string = []
    tmp = {}
    for ip in ip_list:
        tmp['Value'] = ip
        ip_string.append(tmp)
        tmp = {}
    
    return ip_string


if __name__ == '__main__':
    parser = argparse.ArgumentParser()
    
    parser.add_argument('--record_name', required=True,                        help='Value of record name for the record set entry.')
    parser.add_argument('--record_type', required=True, help='Record type.')
    parser.add_argument('--record_ttl', required=True, type=int, help='Record TTL.')
    parser.add_argument('--hosted_zone_id', required=True,                        help='Route 53 hosted zone ID of zone to which this entry will be added.')
    parser.add_argument('--action', required=True, choices=['add', 'remove'],                        help='Whether to add or remove the entry for this machine.')
    parser.add_argument('--testing_ip', help='IP address to use for testing.')
    
    args = parser.parse_args()
    
    if args.testing_ip:
        my_ip = args.testing_ip
    else:
        my_ip = requests.get('http://www.wgetip.com').text
    
    ip_bucket = get_ip_bucket(my_ip)
    ips = get_ips_for_bucket(args.hosted_zone_id, args.record_name, ip_bucket)
    
    already_exists = False    if args.action == 'add':
        
        print(my_ip + ' --- Adding entry in hosted zone %s, record name %s, TTL %s, and IP %s, in record bucket %s.' % (
            args.hosted_zone_id, args.record_name, args.record_ttl, my_ip, ip_bucket))
        
        for item in ips:
            
            if item == my_ip:
                already_exists = True                print(my_ip + ' >>> IP already exists. Exiting.')
                sys.exit(1)
        
        if ~ already_exists:
            print(my_ip + ' >>> IP does not already exist. Adding now.')
            ips.append(my_ip)
        
        values = get_value_string_from_ips(ips)
        
        add_entry(this_ip=my_ip, hosted_zone_id=args.hosted_zone_id, record_bucket=ip_bucket,                  record_set_name=args.record_name,                  record_type=args.record_type,                  record_ttl=args.record_ttl, ip_list=values)
    
    elif args.action == 'remove':
        
        print(
                my_ip + ' --- Removing entry in hosted zone %s, record name %s, TTL %s, and IP %s, in record bucket %s.' % (
                    args.hosted_zone_id, args.record_name, args.record_ttl, my_ip, ip_bucket))
        
        if len(ips) == 1 and ips[0] == my_ip:
            
            print(my_ip + ' >>> This is the only value in the record. Removing entire record.')
            
            values = get_value_string_from_ips(ips)
            
            remove_entry(this_ip=my_ip, hosted_zone_id=args.hosted_zone_id, record_bucket=ip_bucket,                         record_set_name=args.record_name,                         record_type=args.record_type,                         record_ttl=args.record_ttl, ip_list=values)
        else:
            
            if my_ip in ips:
                
                print(my_ip + ' >>> Removing IP from record.')
                
                ips.remove(my_ip)
                values = get_value_string_from_ips(ips)
                
                add_entry(this_ip=my_ip, hosted_zone_id=args.hosted_zone_id, record_bucket=ip_bucket,                          record_set_name=args.record_name,                          record_type=args.record_type,                          record_ttl=args.record_ttl, ip_list=values)
            else:
                print(my_ip + ' >>> IP not present in record.')
    else:
        print(my_ip + ' --- Incorrect action.')
```