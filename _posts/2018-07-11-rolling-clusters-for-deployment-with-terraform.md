---
layout: post
title:  "Rolling clusters for deployment with Terraform"
number: 67
date:   2018-07-11 1:00
categories: automation
---
These notes continue what we've been talking about in [Getting started with Terraform]({% post_url 2017-10-17-getting-started-with-terraform %}) and [Working with Terraform Modules]({% post_url 2018-07-11-working-with-terraform-modules %})

## Recap
In the previous notes we've talked about how to get started with Terraform, why it's a good idea, and how we can use Modules to package our functionality in one place and re-use it as many times as we like. This note will cover how to tackle a different challenge.

## Why do we need to roll our cluster?
Once we've gotten our Terraform-ed infrastructure set up, we can reap the rewards that come with infrastructure-as-code. But what happens when we want to update our code in our cluster?

If you've been following best practices, then our cluster is set up this way

- We use [Packer]({% post_url 2017-09-18-getting-started-with-packer %}) to provision a brand new AMI with our latest code.
- Terraform uses the new Packer AMI to deploy a new cluster.

If we need to update our code, it would involve creating a new AMI using Packer, and then runnning our Terraform, which would destroy the old cluster (since a new AMI triggers a new launch configuration and autoscaling group), launch a new one based on the new AMI, and attach it behind the load balancer.

A sample Terraform file, with some omissions not needed for this example, might look like this:

```json
module "asg" {
  source               = "git@github.com:ayush-sharma/terraform_modules//aws/autoscaling_group/asg_alb"
  name                 = "my-app_${module.launch_config.launch_config_name}"

  min_size                  = "1"
  desired_size              = "1"
  max_size                  = "5"
  health_check_grace_period = "300"
  health_check_type         = "ELB"

  launch_configuration = "${module.launch_config.launch_config_name}"
}

module "launch_config" {
  source          = "git@github.com:ayush-sharma/terraform_modules//aws/autoscaling_group/launch_config"
  ami_filter_name = "packer.my-app.*"
}
```

If you notice something about the above approach, when Terraform destroys the old cluster and creates a new one, there will be a short period of time that our application will be down while the new machines come up. This is our current challange.

In a perfect world, we would want the process to go something like this:

- Bring up new cluster and wait for it to become healthy.
- Attach new cluster behind the load balancer so it can start serving traffic.
- Remove the old cluster from behind the load balancer.
- Destroy the old cluster.

This new "rolling" cluster approach would minimise the time that our application is unavailable. Our new machines will atomic clones of our Packer AMI, so we can have hundreds of new macines come up near instantaneously that are all exactly the same. This is the promise of [immutable infrastructure]({% post_url 2017-09-14-the-different-ways-ive-deployed-code-over-the-years-the-road-to-immutable-servers %}).

## A new approach
In order to implement our perfect scenario, we only have to make a small change to our code:

```json
module "asg" {
  source               = "git@github.com:ayush-sharma/terraform_modules//aws/autoscaling_group/asg_alb"
  name                 = "my-app_${module.launch_config.launch_config_name}"

  min_size                  = "1"
  desired_size              = "1"
  max_size                  = "5"
  health_check_grace_period = "300"
  health_check_type         = "ELB"

  launch_configuration = "${module.launch_config.launch_config_name}"

  wait_for_elb_capacity     = "1"

  lifecycle {
    create_before_destroy = true
  }
}

module "launch_config" {
  source          = "git@github.com:ayush-sharma/terraform_modules//aws/autoscaling_group/launch_config"
  ami_filter_name = "packer.my-app.*"
}
```

Adding 2 new variables will give us what we need:

- The `create_before_destroy = true` directive will create our new autoscaling group before it destroys the old one.
- The `wait_for_elb_capacity` directive will wait for a specified number of instances to become healthy before beginning to destroy our old cluster.

These 2 new directives allows us to safely roll our entire cluster and achieve immutable deployments with minimal downtime. My own experience has been that the process is near realtime, so much so that our monitoring does not pick up on the cluster rotation, but your mileage will vary, and I recommend trying out the approach and getting some hands-on experience before adding this to production. But once you do, life will be that much simpler.

## What next?
Once the above approach works for you and proves meaningful, you can go one step further and set up automated CI/CD pipelines that can now roll your entire infrastructure on code release. Let me know how your experience goes. I can be found on [Mastodon](https://mastodon.technology/@ayushsharma22).

Good luck :)