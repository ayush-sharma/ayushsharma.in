---
layout: post
title:  "Getting started with Terraform"
number: 65
date:   2017-10-17 0:00
categories: automation
---
I recently got some time to play with Terraform, the cloud provisioning software from the same guys as [Vagrant]({% post_url 2016-08-13-introduction-to-vagrant %}) and [Packer]({% post_url 2017-09-18-getting-started-with-packer %}). If you’ve spent some time with Vagrant and Packer, then Terraform will be pretty simple.

In a nutshell, Terraform is a vendor-independent version of AWS Cloudformation. Following infrastructure-as-code principles, it allows you to define your infrastructure resources and their interconnections in a single source file, which Terraform then runs, creating everything you defined in the file. The basic philosophy of Terraform is the same as that for Packer; instead of writing your own scripts to essentially do the same thing on multiple cloud vendors so you can stop worrying about vendor lock-in and cloud migrations, Terraform allows you to use a vendor-independent syntax to create a Terraform file which it can then use to provision your infrastructure on a cloud provider of your choice.

If you haven’t spent time with either [Ansible]({% post_url 2016-08-08-introduction-to-ansible %}) or Cloudformation to automate your infrastructure, then Terraform would be a good starting point. While it doesn’t, and shouldn’t, replace Ansible, which is a configuration management and provisioning tool, it can act as a stand-in for Cloudformation, which is the AWS version of infrastructure-as-code. The beauty of using a vendor-independent tool like Terraform is that you can use it to automate infrastructure provisioning across a variety of cloud providers. Once you’ve defined your infrastructure setup for AWS using Terraform, migrating to Google cloud or Digital ocean might become as simple as changing a few lines of code, instead of figuring out how to do it form the various menu options in the console.

## Installing Terraform
As always, I won’t spend time here. On Mac, run this:

```bash
brew install terraform
```

Next, create a new folder where we’ll be doing our experimentation. Terraform requires a folder for a single project that could contain multiple Terraform definition files, so creating a new folder is a good idea.

## Creating a test instance
Let’s create a test instance in AWS. Create a new file called `test.tf` and place the following in it:

```json
provider "aws" {

    region = "us-east-1"
}

resource "aws_instance" "backend_instances" {
  
    count = 1

    ami           = "ami-cd0f5cb6"
    instance_type = "c4.large"
    subnet_id = "subnet-***"
    vpc_security_group_ids = ["sg-***"]
    key_name = "ayush_production_test"

    tags {
        "Name" = "backend_instance"
    }

    connection {
        type     = "ssh"
        user     = "ubuntu"
        private_key = "${file("key-file.pem")}"
    }

    provisioner "remote-exec" {
        inline = [
            "sudo apt -y update",
            "sudo apt install -y python"
        ]
    }
}
```

Replace the placeholders above with your actual key files, subnets, etc.

The file is pretty simple to read.
- In the first `provider` block, we are specifying some default properties for the AWS provider we’ll be using in this file/deployment. In our case, we’re specifying a default region where all our resources should be created.
- Next is the `resource` block for the `aws_instance`. This defines that this is, well, the resource block for an AWS EC2 instance. Inside this block you can define the usual suspects that are required for launching an AWS instance, like source AMI ID, instance type, etc. Note that the source AMI ID here can be something you can create using Packer, which means Packer and Terraform can be used together to create golden images and then update the entire infrastructure with the new image. The `count` variable indicates how many instances you want. Change this number to 1000, and Terraform will create 1000 new instances for you.
- The `tags` block within the `aws_instance` block defines what tags should be attached to the new instance(s).
- The `connection` block is something Terraform needs so it knows how to connect to new instances, in case it needs to SSH in and do something. We’re using the `${file()}` special variable here which tells Terraform to look for a private key file on the local filesystem on the path provided.
- The `remote-exec` `provisioner` is the additional provisioning step you can run when you launch your instances. If your source AMI ID is already coming from Packer, this step is pretty useless, since the Packer AMI already contains everything you need. But this additional provisioning step can be used to run some last minute commands remotely on your newly launched instances. In this case, we’re using it to install Python.

Once you have all this, just save your file.

The next steps would be to run this file using some `terraform` commands. Note that these commands run on all `*.tf* files you define in the current working directory.

Let’s start.
- Run `terraform init` in your working directory. This step will download any plugins that are needs for the providers you are using. The output should look something like this:

```bash
Initializing provider plugins...
- Checking for available provider plugins on https://releases.hashicorp.com...
- Downloading plugin for provider "aws" (1.1.0)...

The following providers do not have any version constraints in configuration,
so the latest version was installed.

To prevent automatic upgrades to new major versions that may contain breaking
changes, it is recommended to add version = "..." constraints to the
corresponding provider blocks in configuration, with the constraint strings
suggested below.

* provider.aws: version = "~> 1.1"

Terraform has been successfully initialized!
```

- Next, run `terraform plan`. This step should give you an output indicating what Terraform is actually going to end up creating using the steps we defined above. The executing plan will indicate what parameters you have specified for this instance, and which ones its going to calculate after launch.
- Once you’re happy with your executing plan, which should only indicate the creation of one instance, run `terraform apply`, and then sit back while Terraform brings up a new EC2 instance for you.

## Creating a load balancer
Let’s do something a bit more complicated. Add the following lines to the `test.tf` files we created above:

```json
resource "aws_elb" "backend-elb" {

    name = "backend-load-balancer"

    listener {
        instance_port     = 80
        instance_protocol = "http"
        lb_port           = 80
        lb_protocol       = "http"
    }

    health_check {
        healthy_threshold   = 2
        unhealthy_threshold = 2
        timeout             = 5
        target              = "TCP:80"
        interval            = 30
    }

    cross_zone_load_balancing   = true
    idle_timeout                = 400
    connection_draining         = true
    connection_draining_timeout = 400

    subnets = ["subnet-***"]

    instances = ["${aws_instance.backend_instances.*.id}"]
}
```

So here, we’re creating a new AWS load balancer with the usual parameters it requires. The name, listener configurations, health check configurations, etc. The last line starting with `instances…` is new. This line is actually telling Terraform to take all instances we launched using the “aws_instance” resource above, and add them behind this load balancer. Note that the `backend_instances` used in this line is the same as the name we used for the AWS instance resource before. Terraform is smart enough to figure out that it needs to create the instances before it can attach them to the load balancer, so it will create and launch the instances first and make the load balancer second. Check your execution plan again by running `terraform plan`, and you should see a new load balancer being created.

This is just a basic warm-up of Terraform and should serve as a PoC. The executing plan feature is really great for reviewing the final changes to be made on the cluster, and will certainly take care of a lot of deployment headaches. By having this all in git, change control and review will be must faster. And once the infrastructure is well-defined is source code, replicating it to create staging and testing environments will be all that easier.

When you run Terraform, it will create some additional files in your local directory to keep track of what it created. It will use this on subsequent runs when you want to update or delete resources.