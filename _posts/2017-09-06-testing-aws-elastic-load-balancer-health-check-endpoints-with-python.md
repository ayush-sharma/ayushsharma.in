---
layout: post
title:  "Testing AWS Elastic Load Balancer health check endpoints with Python"
number: 52
date:   2017-09-06 0:00
categories: networking
---
Wrote a little script to poll all the AWS Elastic Load Balancer instances and check the health check endpoints. For example, if your health check endpoint is `/healthcheck`, this script will get the public IPs of all the ELB instances, and hit the endpoint `/healthcheck` at those IPs to ensure they’re being properly forwarded to your backend instances. Should return HTTP status code 200.

Usage:

```bash
python3 aws_elb_check_elb.py --elb_name http://elb.region.elb.amazon.aws.com --path healthcheck
```

Source can be found [here](https://github.com/ayush-sharma/infra_helpers/blob/master/aws/aws_elb_check_elb.py).

```python
import argparse
import requests
import dns.resolver


if __name__ == '__main__':
    parser = argparse.ArgumentParser()

    parser.add_argument('--elb_name', required=True, help='Value of ELB DNS.')
    parser.add_argument('--path', required=True,
                        help='Path to test for HTTP status code 200.')

    args = parser.parse_args()

    url = 'all.' + args.elb_name

    answers = dns.resolver.query(url, 'A')
    count = 1
    for ip in answers:

        ip = str(ip)
        test_request = 'http://' + ip + '/' + args.path

        request = requests.get(test_request)

        print(str(count) + '. ' + test_request + ' > ' + str(request.status_code))

        count += 1
```