---
layout: post
title:  "Using Packer and Ansible to create immutable servers, deploying code, and recycling instances"
number: 55
date:   2017-09-18 1:00
categories: automation
---
In my last two posts, I covered [why immutable servers are important]({% post_url 2017-09-14-the-different-ways-ive-deployed-code-over-the-years-the-road-to-immutable-servers %}), and [how to use Packer to dynamically create AMIs]({% post_url 2017-09-18-getting-started-with-packer %}). In this post, I’ll cover how to use Packer and Ansible to deploy code and recycle machines.

Let’s say we have a cluster of machines running in a particular autoscaling group. This cluster would be using a particular launch configuration and AMI to launch new machines. Traditionally, we would use an Ansible script to deploy new code to these machines, but [as I’ve already covered]({% post_url 2017-09-14-the-different-ways-ive-deployed-code-over-the-years-the-road-to-immutable-servers %}), that approach has some pitfalls. So we’re going to try another approach.

Let’s say we want to deploy a particular tag from our repo onto our machines. Our new approach would be this:

1. Use Packer to launch temporary instance from source AMI.
2. Deploy code tag onto temporary machine.
3. Create new AMI from machine.
4. Create new launch configuration for the new AMI.
5. Update launch configuration in autoscaling group.
6. Recycle existing machines; launch machines from new launch configuration, and terminate existing machines which are using old launch configuration.

We will be using Packer to run steps 1 to 3, and Ansible to run 4 to 6.

Let’s begin.

## The Packer build and provisioning files
Consider the following Packer build file `my_test_build.json`:

```bash
{
    "builders": [
      {
        "type": "amazon-ebs",
        "region": "us-east-1",
        "source_ami": "ami-cd0f5cb6",
        "instance_type": "m3.large",
        "ssh_username": "ubuntu",
        "associate_public_ip_address": true,
        "ami_name": “packer.my_test_machine.{% raw %}{{timestamp}}{% endraw %}",
        "tags": {
          "Name": "packer.my_test_machine.{% raw %}{{timestamp}}{% endraw %}",
          "source": "packer"
        }
      }
    ],
    "provisioners": [
      {
        "type": "shell",
        "execute_command": "chmod +x {% raw %}{{ .Path }}{% endraw %}; sudo -S sh -c '{% raw %}{{ .Vars }}{% endraw %} {% raw %}{{ .Path }}{% endraw %} {% raw %}{{ user `code_tag`}}{% endraw %}'",
        "script": "packer_provision.sh"
      }
    ]
  }

```

The above file will be familiar from the last post on [Packer]({% post_url 2017-09-18-getting-started-with-packer %}). The one new line is `execute_command`. This line specifies how the provisioning file provided in `script` is executed. In this case, we’re writing the command such that the file is executed with `sudo` privileges, and that the environment variable `code_tag` is passed on to the script. `{% raw %}{{{% endraw %} user code_tag{% raw %}}}{% endraw %}` is just Packer syntax instructing it to use the user variable `code_tag`. We will pass this parameter to Packer when we execute the build command, which we can then use in the provisioning script. In this case, we will be passing the git tag of the code we want deployed.

Consider the provisioning script `packer_provision.sh` below:

```bash
#!/bin/bash

set -x

code_tag=$1

apt update

apt -y install nginx

git clone https://github.com/ayush-sharma/infra_helpers
cd infra_helpers/
git checkout $code_tag
```

The above provisioning script will take the code tag to be deployed as a parameter which Packer will provide. It will then use this tag to checkout that tag on the temporary Packer instance and then package it as an AMI. Which means machines that come up from this AMI will already have that tag. I’m using my own repo as an example, but this could be any repo that you use. If your repo requires access credentials, you can set them up in the environment (recommended and more secure), or in the provisioning script itself.

The Packer build file, along with the code tag to use, can be called using the following command:

```bash
packer build -var 'code_tag=master' my_test_build.json
```

This file will give us the new AMI ID at the end of its run, which we can get using `tail` and `grep` later on.

## Ansible launch configuration update and instance recycling
So.

The above Packer build file and provisioning script will give us the fully provisioned AMI, accomplishing steps 1 to 3, and now we need to deploy this AMI to new and existing instances.

For example, running the Packer build and getting the new AMI ID in Ansible might look like this:

```yaml
- name: Run Packer build for creating AMI
  shell: packer build -var 'code_tag={{ tag }}' my_test_build.json | tail -1 | grep "ami-" | awk '{print $2}'
  register: my_ami
```

Note that we’re using `tail` and `grep` to get the AMI ID that Packer returns as a result. We’re also registering the new AMI ID in the Ansible variable `my_ami` so we can use it later.

Once we have the AMI ID above, we can use it to create a new launch configuration:

```yaml
- name: Creating launch configuration
  ec2_lc:
    name: "nginx_packer_{% raw %}{{timestamp.stdout}}{% endraw %}"
    region: "{% raw %}{{region}}{% endraw %}"
    image_id: "{% raw %}{{ my_ami.stdout }}{% endraw %}"
    assign_public_ip: "yes"
    key_name: "{% raw %}{{ key_name }}{% endraw %}"
    security_groups: "{% raw %}{{ security_groups }}{% endraw %}"
    instance_type: m3.large
    volumes:
    - device_name: /dev/sda1
      volume_size: 50
      device_type: gp2
      iops: 150
      delete_on_termination: true
      encrypted: false
  register: ec2_lc_info
```

The above YAML will create a new launch configuration and place the details within `ec2_lc_info`, which we can then use to update the autoscaling group. Ignore some of the other variables for now, I’ll explain those later. To update the autoscaling group, you can use this:


```yaml
- name: Updating ASG
  ec2_asg:
    name: "{% raw %}{{ asg }}{% endraw %}"
    region: "{% raw %}{{ region }}{% endraw %}"
    load_balancers: "{% raw %}{{ elb }}{% endraw %}"
    availability_zones: [ 'us-east-1a' ]
    vpc_zone_identifier: ['subnet-xxxxx']
    launch_config_name: "{% raw %}{{ ec2_lc_info.name }}{% endraw %}"
    termination_policies: ['OldestLaunchConfiguration']
    state: present
    min_size: "{% raw %}{{ my_asg_info.results[0].desired_capacity }}{% endraw %}"
    max_size: 300
    desired_capacity: "{% raw %}{{ my_asg_info.results[0].desired_capacity }}{% endraw %}"
    replace_all_instances: yes
    replace_batch_size: "{% raw %}{{ (my_asg_info.results[0].desired_capacity % 10) | int | abs }}{% endraw %}"
    wait_for_instances: True
```

The above YAML will cause Ansible to update the new launch configuration we created in the autoscaling group we specify, and also to recycle old instances.

## Complete Ansible script
The entire Ansible playbook, called `deploy.yml`, will look something like this:

```yaml
---
- hosts: localhost
  connection: local
  gather_facts: no
  vars:
    region: "us-east-1"
    elb: "my_nginx_elb"
    asg: "my_nginx_asg"
    key_name: "my_private_key_name"
    security_groups: 'sg-xxxxx'
  tasks:
  - name: Getting Timestamp
    shell: echo `date +%Y-%m-%d-%H-%M-%S`
    register: timestamp
  - name: Run Packer build for creating AMI
    shell: packer build -var 'code_tag={% raw %}{{ tag }}{% endraw %}' my_test_build.json | tail -1 | grep "ami-" | awk '{print $2}'
    register: my_ami
  - name: AMI name
    debug: msg={% raw %}{{ my_ami.stdout }}{% endraw %}
  - name: Creating launch configuration
    ec2_lc:
      name: "nginx_packer_{% raw %}{{timestamp.stdout}}{% endraw %}"
      region: "{% raw %}{{region}}{% endraw %}"
      image_id: "{% raw %}{{ my_ami.stdout }}{% endraw %}"
      assign_public_ip: "yes"
      key_name: "{% raw %}{{ key_name }}{% endraw %}"
      security_groups: "{% raw %}{{ security_groups }}{% endraw %}"
      instance_type: m3.large
      volumes:
      - device_name: /dev/sda1
        volume_size: 50
        device_type: gp2
        iops: 150
        delete_on_termination: true
        encrypted: false
    register: ec2_lc_info
  - name: Get current ASG info
    ec2_asg_facts:
      name: "{% raw %}{{ asg }}{% endraw %}"
      region: "{% raw %}{{ region }}{% endraw %}"
    register: my_asg_info
  - name: Updating ASG
    ec2_asg:
      name: "{% raw %}{{ asg }}{% endraw %}"
      region: "{% raw %}{{ region }}{% endraw %}"
      load_balancers: "{% raw %}{{ elb }}{% endraw %}"
      availability_zones: [ 'us-east-1a' ]
      vpc_zone_identifier: ['subnet-xxxxx']
      launch_config_name: "{% raw %}{{ ec2_lc_info.name }}{% endraw %}"
      termination_policies: ['OldestLaunchConfiguration']
      state: present
      min_size: "{% raw %}{{ my_asg_info.results[0].desired_capacity }}{% endraw %}"
      max_size: 300
      desired_capacity: "{% raw %}{{ my_asg_info.results[0].desired_capacity }}{% endraw %}"
      replace_all_instances: yes
      replace_batch_size: "{% raw %}{{ (my_asg_info.results[0].desired_capacity % 10) | int | abs }}{% endraw %}"
      wait_for_instances: True
```

Note that we’re using `ec2_asg_facts` to get the current autoscaling group instance numbers, and providing them to `ec2_asg` to rotate instances. The `replace_all_instances` parameter to `ec2_asg` will tell it to replace currently running instances by launching new ones, waiting for them to become healthy, and them terminating old ones that do not have the new launch configuration. You can configure the batch size of the new machines launched using `replace_batch_size`, but what I’m using above will cause it to replace instances in 10 rotations, by taking the modulus of the current desired capacity.

Also note that the Ansible script takes a parameter of `{% raw %}{{ tag }}{% endraw %}` which it then passes to Packer as the value of `code_tag`. The above Ansible script can be run using:

```bash
ansible-playbook deploy.yml -e 'tag=master'
```

The above Ansible script may take some time to run, depending on how many machines you currently have in production, but at the end of it, all of your machines should be running on a new AMI.

Next steps? You can put this new Ansible script in a cron job to [create phoenix servers]({% post_url 2017-09-14-the-different-ways-ive-deployed-code-over-the-years-the-road-to-immutable-servers %}) or [use a CI/CD pipeline to trigger new builds on code commits]({% post_url 2017-08-25-deploying-jekyll-blog-automatically-using-bitbucket-pipelines %}).

Thanks for staying tuned. Happy coding :)