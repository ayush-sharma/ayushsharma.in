---
layout: post
title:  "Working with Terraform Modules"
number: 66
date:   2018-07-11 0:00
categories: automation
---
Terraform has become a widely-used and vendor-agnostic tool for setting up infrastructure. The simple idea has appealed to many developers: [declare the infrastructure that you need and it is either created or updated to what you define]({% post_url 2017-10-17-getting-started-with-terraform %}). This simple approach gives us two powerful advantages:

- Our infrastructure is finally in a set of files which we can commit in to source control. And once that happens, we can have all of the goodies that come with source control, like code reviews, separate feature branches, etc.
- We also no longer need to document our infrastructure since it is now source code rather than a sequence of steps we need to perform on a web interface.

I’m sure you can already see a lot more advantages of having your infrastructure in source code. But with these new advantages we also get a few challenges.

## Our new challenge
One of the most obvious issues becomes code re-usability. For example, let’s say we need to set up 3 different load-balancers. Should we get one load balancer up and running and then copy-paste that code for the other two? This approach seems risky and error-prone. Fortunately, we have a solution.

## Re-using infrastructure components with Modules
A Terraform Module is conceptually the same as a function in a programming language. For example, if I need a program to add a set of numbers, it makes sense for me to write a function to add numbers, test it and make sure it works, and then pass this function the parameters I need and calculate the result.

A Terraform Module works the same way. It allows me to create a function/module for creating an infrastructure component, then pass this module the parameters for different components. This way, I can write code once and test it, and use it safely as many times as possible.

To create a load-balancer, the Terraform script would look like this:

```json
resource "aws_alb" "alb" {
  name                       = "my-load-balancer"
  subnets                    = ["${var.subnets}"]
  security_groups            = ["${var.security_groups}"]
  internal                   = "true"
  idle_timeout               = "300"
  tags                       = "${var.tags}"
  access_logs                = "false"
  load_balancer_type         = "application"
  enable_deletion_protection = "true"
}
```

To use this code to create 3 more load-balancers, I can either copy-paste this code 3 times and change the names and parameters, or I can convert it to a module and call that module 3 times. My new load-balancer module would look like this:

```json
resource "aws_alb" "alb" {
  name                       = "${var.name}"
  subnets                    = ["${var.subnets}"]
  security_groups            = ["${var.security_groups}"]
  internal                   = "false"
  idle_timeout               = "${var.idle_timeout}"
  tags                       = "${var.tags}"
  access_logs                = ["${var.access_logs}"]
  load_balancer_type         = "application"
  enable_deletion_protection = "true"
}
```

Now that I have this module, I can call it multiple times to create as many load balancers as I want:

```json
module "alb" {
  source = "git@github.com:ayush-sharma/terraform_modules//aws/alb/alb"

  name    = "my-load-balancer-1"
  subnets = "${var.subnets}"

  security_groups = [
    "${module.elb_security_group.sg_id}",
  ]

  internal     = false
  idle_timeout = "300"

  tags = {
    "Name"        = "my-load-balancer-1"
    "cluster"     = "my-cluster-1"
    "cost_center" = "my-cost-center"
    "created_by"  = "Terraform"
  }

  enable_deletion_protection = true
}
```

If you notice the above code, the module is called using the `source` line which specifies where my module is. In this case, I’m loading my module from a public git repo (more on this later). On running `terraform init`, the module will be downloaded for re-use in our Terraform file.

## Running with modules
Conceptually modules are no different from functions in a programming language, and encapsulate functionality so it can be re-used as many times as we like.

Let's take another example. Let's say we want to create CloudWatch alarms for all of our load balancers. But creating alarms aren't easy since we would have to create about 10 alarms for every load balancer we have. Can modules help in this case?

Consider the following Terraform file `my_cloudwatch_alarms/main.tf`:

```json
module "alb_no_healthy_hosts" {
  source              = "git@github.com:ayush-sharma/terraform_modules//aws/cloudwatch_metric_alarm"
  alarm_name          = "${var.app_name}_alb_no_healthy_host"
  comparison_operator = "LessThanOrEqualToThreshold"
  evaluation_periods  = "1"
  metric_name         = "HealthyHostCount"
  namespace           = "AWS/ApplicationELB"
  period              = "60"
  statistic           = "Average"
  threshold           = "0"
  alarm_description   = "This alarm triggers when ELB contains no healthy hosts."

  dimensions = {
    LoadBalancer = "${var.alb_arn_suffix}"
  }

  alarm_actions = [
    "${var.alert_sns_topic}",
  ]

  insufficient_data_actions = []
}

module "alb_high_unhealthy_hosts" {
  source              = "git@github.com:ayush-sharma/terraform_modules//aws/cloudwatch_metric_alarm"
  alarm_name          = "${var.app_name}_alb_high_unhealthy_host"
  comparison_operator = "GreaterThanOrEqualToThreshold"
  evaluation_periods  = "1"
  metric_name         = "UnHealthyHostCount"
  namespace           = "AWS/ApplicationELB"
  period              = "60"
  statistic           = "Maximum"
  threshold           = "1"
  alarm_description   = "This alarm triggers when unhealthy instances are high."

  dimensions = {
    LoadBalancer = "${var.alb_arn_suffix}"
  }

  alarm_actions = [
    "${var.alert_sns_topic}",
  ]

  insufficient_data_actions = []
}

module "alb_latency_high" {
  source              = "git@github.com:ayush-sharma/terraform_modules//aws/cloudwatch_metric_alarm"
  alarm_name          = "${var.app_name}_alb_high_latency"
  comparison_operator = "GreaterThanOrEqualToThreshold"
  evaluation_periods  = "1"
  metric_name         = "TargetResponseTime"
  namespace           = "AWS/ApplicationELB"
  period              = "60"
  statistic           = "Average"
  threshold           = "1"
  alarm_description   = "This alarm triggers when ELB latency is high."

  dimensions = {
    LoadBalancer = "${var.alb_arn_suffix}"
  }

  alarm_actions = [
    "${var.alert_sns_topic}",
  ]

  insufficient_data_actions = []
}

module "alb_4xx_high" {
  source              = "git@github.com:ayush-sharma/terraform_modules//aws/cloudwatch_metric_alarm"
  alarm_name          = "${var.app_name}_alb_high_alb_4xx"
  comparison_operator = "GreaterThanOrEqualToThreshold"
  evaluation_periods  = "1"
  metric_name         = "HTTPCode_ELB_4XX_Count"
  namespace           = "AWS/ApplicationELB"
  period              = "60"
  statistic           = "Sum"
  threshold           = "10"
  alarm_description   = "This alarm triggers when ELB 4XX count is high."

  dimensions = {
    LoadBalancer = "${var.alb_arn_suffix}"
  }

  alarm_actions = [
    "${var.alert_sns_topic}",
  ]

  insufficient_data_actions = []
}

module "alb_5xx_high" {
  source              = "git@github.com:ayush-sharma/terraform_modules//aws/cloudwatch_metric_alarm"
  alarm_name          = "${var.app_name}_alb_high_alb_5xx"
  comparison_operator = "GreaterThanOrEqualToThreshold"
  evaluation_periods  = "1"
  metric_name         = "HTTPCode_ELB_5XX_Count"
  namespace           = "AWS/ApplicationELB"
  period              = "60"
  statistic           = "Sum"
  threshold           = "10"
  alarm_description   = "This alarm triggers when ELB 5XX count is high."

  dimensions = {
    LoadBalancer = "${var.alb_arn_suffix}"
  }

  alarm_actions = [
    "${var.alert_sns_topic}",
  ]

  insufficient_data_actions = []
}

module "alb_target_4xx_high" {
  source              = "git@github.com:ayush-sharma/terraform_modules//aws/cloudwatch_metric_alarm"
  alarm_name          = "${var.app_name}_alb_high_target_4xx"
  comparison_operator = "GreaterThanOrEqualToThreshold"
  evaluation_periods  = "1"
  metric_name         = "HTTPCode_Target_4XX_Count"
  namespace           = "AWS/ApplicationELB"
  period              = "60"
  statistic           = "Sum"
  threshold           = "10"
  alarm_description   = "This alarm triggers when target 4XX count is high."

  dimensions = {
    LoadBalancer = "${var.alb_arn_suffix}"
  }

  alarm_actions = [
    "${var.alert_sns_topic}",
  ]

  insufficient_data_actions = []
}

module "alb_target_5xx_high" {
  source              = "git@github.com:ayush-sharma/terraform_modules//aws/cloudwatch_metric_alarm"
  alarm_name          = "${var.app_name}_alb_high_target_5xx"
  comparison_operator = "GreaterThanOrEqualToThreshold"
  evaluation_periods  = "1"
  metric_name         = "HTTPCode_Target_5XX_Count"
  namespace           = "AWS/ApplicationELB"
  period              = "60"
  statistic           = "Sum"
  threshold           = "10"
  alarm_description   = "This alarm triggers when target 5XX count is high."

  dimensions = {
    LoadBalancer = "${var.alb_arn_suffix}"
  }

  alarm_actions = [
    "${var.alert_sns_topic}",
  ]

  insufficient_data_actions = []
}

module "alb_rejected_connections_high" {
  source              = "git@github.com:ayush-sharma/terraform_modules//aws/cloudwatch_metric_alarm"
  alarm_name          = "${var.app_name}_alb_high_rejected_connections"
  comparison_operator = "GreaterThanOrEqualToThreshold"
  evaluation_periods  = "1"
  metric_name         = "RejectedConnectionCount"
  namespace           = "AWS/ApplicationELB"
  period              = "60"
  statistic           = "Sum"
  threshold           = "10"
  alarm_description   = "This alarm triggers when rejected connections are high."

  dimensions = {
    LoadBalancer = "${var.alb_arn_suffix}"
  }

  alarm_actions = [
    "${var.alert_sns_topic}",
  ]

  insufficient_data_actions = []
}

module "alb_target_connection_error_high" {
  source              = "git@github.com:ayush-sharma/terraform_modules//aws/cloudwatch_metric_alarm"
  alarm_name          = "${var.app_name}_alb_high_connection_errors"
  comparison_operator = "GreaterThanOrEqualToThreshold"
  evaluation_periods  = "1"
  metric_name         = "TargetConnectionErrorCount"
  namespace           = "AWS/ApplicationELB"
  period              = "60"
  statistic           = "Sum"
  threshold           = "10"
  alarm_description   = "This alarm triggers when target connection errors are high."

  dimensions = {
    LoadBalancer = "${var.alb_arn_suffix}"
  }

  alarm_actions = [
    "${var.alert_sns_topic}",
  ]

  insufficient_data_actions = []
}

module "alb_ssl_connection_error_high" {
  source              = "git@github.com:ayush-sharma/terraform_modules//aws/cloudwatch_metric_alarm"
  alarm_name          = "${var.app_name}_alb_high_ssl_connection_errors"
  comparison_operator = "GreaterThanOrEqualToThreshold"
  evaluation_periods  = "1"
  metric_name         = "TargetTLSNegotiationErrorCount"
  namespace           = "AWS/ApplicationELB"
  period              = "60"
  statistic           = "Sum"
  threshold           = "10"
  alarm_description   = "This alarm triggers when SSL connection errors are high."

  dimensions = {
    LoadBalancer = "${var.alb_arn_suffix}"
  }

  alarm_actions = [
    "${var.alert_sns_topic}",
  ]

  insufficient_data_actions = []
}
```

In the file above, I have 10 CloudWatch application load-balancer alarms configured. Notice the following:

- Each of the alarm relies on the same common module.
- Each alarm only has 3 configurable values: the application name, the load-balancer ARN, and a SNS topic to send alerts to.

Now we can call the above module like this:

```json
module "all_alb_cloudwatch_alarms" {
  source          = "my_cloudwatch_alarms"
  app_name        = "my-application-name"
  alb_arn_suffix  = "my-load-balancer-arn"
  alert_sns_topic = "my-sns-topic"
}
```

Using the above few lines of code, we can apply 10 CloudWatch alarms at once to any new load-balancer we create. We can encapsulate any kind of logic we want and re-use it multiple times.

## In Conclusion
In this example, I loaded the modules from my own public github repo located [here](/projects). You can find a lot of open-source Terraform modules for your needs, and chances are good that you won’t have to write modules for common things. Code re-use is also code-sharing, so make your life easier by researching what others have done, and don’t forget to share :)