---
layout: post
title:  "Getting started with Packer"
number: 54
date:   2017-09-18 0:00
categories: automation
---
If you followed [my last post on the different deployment types]({% post_url 2017-09-14-the-different-ways-ive-deployed-code-over-the-years-the-road-to-immutable-servers %}), you’ll know that I talked about immutable servers. The idea behind immutable servers is that once provisioned, the servers should not be interfered with in any way, either for code updates or configuration changes. Any required changes to the server must recreate the server from scratch, instead of logging in and making changes.

One way to accomplish this is to leverage AWS AMIs, or Amazon Machine Images. An AMI is a point-in-time snapshot of the current state of the server, which includes a snapshot of the underlying operating system and the disks attached to them. AMIs can be created using AWS SDKs in the language of your choice, but there a more vendor-independent option that also provides more features.

I’m going to spend this post talking about Packer.

## What is Packer?
Packer is a tool for creating golden images from which you can launch new instances. You can create these images from any base image, which can either be a fresh operating system or the snapshot of a currently running machine. Once you have this base image, you can configure additional software by using various provisioners available, like bash or Ansible. Once this is done, you could just run Packer, which would bring up a temporary instance, run the provisioning steps you specify, create an image of this temporary instance, and then perform clean up steps, such as destroying the temporary instance.

The instructions you need Packer to run can be written using JSON. This enables you to declaratively specify what you need done and have Packer take care of it, instead of writing code to actually do it yourself.

## Builders and Provisioners
Two of the basic concepts in Packer are builders and Provisioners.

Builders are responsible for the actual heavy lifting, which is creating the images. Builders allow you to use a relatively uniform syntax to create images for AWS, Google Cloud, DigitalOcean, and various other platforms. Builders define which base image to use, and how to configure the temporary instance Packer will use to create the target image that you need.

Provisioners are what you will use to install additional software on top of your base image. Without provisioning, simply using Packer to create a new image from a base image would be quite useless. Provisioning is the middle step where you can install and configure whatever you want, and Packer allows you to use different kinds of provisioners in this step, like bash or Ansible.

## Getting Started
You can find the installation steps for Packer [here](https://www.packer.io/docs/install/index.html).

Once installed, running packer is as simple as `packer build <build-file>`, which will take the `build-file` and run the steps we provide within. Let’s get started with a simple build file.

## The Build file and the Provisioning file
Save the file below as `my_test_build.json`.

```json
{
    "builders": [
      {
        "type": "amazon-ebs",
        "region": "us-east-1",
        "source_ami": "ami-cd0f5cb6",
        "instance_type": "m3.large",
        "ssh_username": "ubuntu",
        "associate_public_ip_address": true,
        "ami_name": "packer.my_test_machine.{% raw %}{{timestamp}}{% endraw %}",
        "tags": {
          "Name": "packer.my_test_machine.{% raw %}{{timestamp}}{% endraw %}",
          "source": "packer"
        }
      }
    ],
    "provisioners": [
      {
        "type": "shell",
        "script": "packer_provision.sh"
      }
    ]
  }
```

Save the file below as `packer_provision.sh`:

```bash
#!/bin/bash

set -x

apt update

apt -y install nginx
```

Let’s go over what these files are doing.

The build file `my_test_build.json` contains two top level sections, `builders` and `provisioners`. In the `builders` section, we specify the details of the platform for which we want to build the image. In the `provisioners` section, we specify additional installation and configuration steps that we want to bake into the AMI.

In the `builders` section:
- We’re specifying the `amazon-ebs` builder in `type`, which will tell Packer that this build file needs to build an EBS image for the AWS platform.
- `region` tells Packer in which region it should launch the temporary instance it needs to create the image.
- `source_ami` is the AMI ID of the base image that Packer will use to launch the temporary instance. We can use the provisioning step to install additional things onto this base AMI. In our example above, the base AMI ID used is that of the default Ubuntu 16.04, which is mentioned in AWS as "Ubuntu Server 16.04 LTS (HVM)”.
- `instance_type` is the instance type that Packer should use for its temporary instance. If you have a lot of provisioning to do, use a larger instance type. Note that some instance types can only be launched within VPCs, which you will have to separately configure within the `builder` section.
- `ssh_username` tells Packer what to use as the SSH username for logging in to the temporary machine.
- `associate_public_ip_address` decides whether the temporary instance has a public IP address or not. This setting will depend on where you’re using Packer from. This setting might not be necessary if you’re using Packer over a private network.
- `ami_name` is the name of the target AMI that Packer will create after provisioning. `\{\{timestamp\}\}` is automatically replace by the current UNIX timestamp in UTC, which helps make this AMI unique if you’ll be running it multiple times. Packer provides more functions to use with AMI names.
- `tags` are AWS tags which Packer will attach to the target AMI.

In the `provisioners` section:
- We’re using the `shell` type provisioner in `type` and specifying the path of the shell file (in `script`) that we want Packer to run after it launches the temporary machine from the base AMI. Packer will run the instructions in this file, then stop the instance and create an image from it. Once that’s complete, it will perform a cleanup and destroy the temporary instance.

## Running it
Once we have the above files, let’s run it using the following command:

```bash
packer build my_test_build.json
```

Once the file is done running, and it shouldn’t take long, you’ll see the output below:

```bash
amazon-ebs output will be in this color.

==> amazon-ebs: Prevalidating AMI Name...
    amazon-ebs: Found Image ID: ami-cd0f5cb6
==> amazon-ebs: Creating temporary keypair: packer_59bd0995-1b93-a3e7-53a7-1df45d34176e
==> amazon-ebs: Creating temporary security group for this instance: packer_59bd099a-e84d-3a3d-0a9e-519ddb4c4ebb
==> amazon-ebs: Authorizing access to port 22 on the temporary security group...
==> amazon-ebs: Launching a source AWS instance...
    amazon-ebs: Instance ID: i-xxxxx
==> amazon-ebs: Waiting for instance (i-xxxxx) to become ready...
==> amazon-ebs: Adding tags to source instance
    amazon-ebs: Adding tag: "Name": "Packer Builder"
==> amazon-ebs: Waiting for SSH to become available...
==> amazon-ebs: Connected to SSH!
==> amazon-ebs: Provisioning with shell script: packer_provision.sh
```

If you see above, Packer is launching a temporary instance from the base AMI ID we provided, creating a temporary key pair and security group so it can SSH into the instance, and is then running the provisioning script we provided. It’s even naming the temporary instance as `Packer Builder` so you can find it using the AWS console.

Packer will then begin executing the steps in our provisioning script. I won’t print the output here since its huge, but if the provisioning steps runs successfully, you should see this:


```bash
==> amazon-ebs: Stopping the source instance...
    amazon-ebs: Stopping instance, attempt 1
==> amazon-ebs: Waiting for the instance to stop...
==> amazon-ebs: Creating the AMI: packer.my_test_machine.1505560981
    amazon-ebs: AMI: ami-xxxxx
==> amazon-ebs: Waiting for AMI to become ready...
==> amazon-ebs: Adding tags to AMI (ami-xxxxx)...
==> amazon-ebs: Tagging snapshot: snap-xxxxx
==> amazon-ebs: Creating AMI tags
    amazon-ebs: Adding tag: "source": "packer"
    amazon-ebs: Adding tag: "Name": "packer.my_test_machine.1505560981"
==> amazon-ebs: Creating snapshot tags
==> amazon-ebs: Terminating the source AWS instance...
==> amazon-ebs: Cleaning up any extra volumes...
==> amazon-ebs: No volumes to clean up, skipping
==> amazon-ebs: Deleting temporary security group...
==> amazon-ebs: Deleting temporary keypair...
Build 'amazon-ebs' finished.

==> Builds finished. The artifacts of successful builds are:
--> amazon-ebs: AMIs were created:

us-east-1: ami-xxxxx
```

As you can see, after the provisioning, it stopped the instance, created the AMI from it, and then applied the tags to the AMI that we specified. It also cleaned up the temporary instance, its volumes, and the key pair and security group it created earlier. At the bottom, it will output the ID of the target AMI and the region where it was made.

## Conclusion
You can now use the target AMI Packer gave you to launch the machine, and it will have the base image + provisioning. This is a great way to launch pre-baked machines that have the latest source code and configuration already in them, which reduces the time for new machines to launch.

Remember to check out the other builders and provisioners that Packer provides to ensure how it can fit with your existing tool chain. Packer also has a post-processing concept, which allows you to do a variety of things with images once they’re made, such as compressing them and uploading them somewhere.

## Resources
- [#53: The different ways I've deployed code over the years: the road to Immutable Servers]({% post_url 2017-09-14-the-different-ways-ive-deployed-code-over-the-years-the-road-to-immutable-servers %}).
- [Packer website](https://www.packer.io/).
- [Packer docs](https://www.packer.io/docs/index.html).