---
layout: post
title:  "Multi-machine Setup and Configuration with Vagrant"
number: 19
date:   2016-08-27 0:00
categories: automation
---
You might want to go over the [Introduction to Vagrant]({% post_url 2016-08-13-introduction-to-vagrant %}) and [Provisioning with Vagrant]({% post_url 2016-08-15-provisioning-with-vagrant %}) posts.

Instead of just playing around with one machine, Vagrant allows you to define multiple machines in one Vagrantfile and configure them accordingly. Let's create an example Vagrantfile where we define one master and two slaves, along with some networking and shell provisioning. The Vagrantfile would look like this:

```ruby
Vagrant.configure("2") do |config|

  config.vm.define "master" do |master|

    master.vm.hostname = "mm-master"
    master.vm.box = "ubuntu/trusty64"
    master.vm.network "private_network", ip: "192.168.10.1"

    master.vm.provision "shell",
      inline:"apt-get update; apt-get -y install nginx"
  end

  config.vm.define "slave_1" do |slave_1|

    slave_1.vm.hostname = "mm-slave-1"
    slave_1.vm.box = "ubuntu/trusty64"
    slave_1.vm.network "private_network", ip: "192.168.10.2"

    slave_1.vm.provision "shell",
      inline:"apt-get update; apt-get -y install nginx"
  end

  config.vm.define "slave_2" do |slave_2|

    slave_2.vm.hostname = "mm-slave-2"
    slave_2.vm.box = "ubuntu/trusty64"
    slave_2.vm.network "private_network", ip: "192.168.10.3"

    slave_2.vm.provision "shell",
      inline:"apt-get update; apt-get -y install nginx"
  end
end
```

One thing to remember is that the order of execution is outside -> in. First the outermost commands will get processed, then the ones deeper in.

## Vagrant Commands
The usual Vagrant commands work fine. By default, they work on all of the machines you made in the Vagrantfile. But you can make them work on a single machine by specifying its name.

- `vagrant ssh <machine-name>`: Simply doing ssh won't work anymore. You'll have to specify the name of the machine you want to ssh into.
- `vagrant up` or `vagrant up <machine-name>`: You can bring up all machines at once or one specific machine. There is also an option to specify which machines should come up when you do `up` without a machine name.
- `vagrant status` or `vagrant status <machine-name>`: Show the status of all machines or a specific one.
- `vagrant suspend` or `vagrant suspend <machine-name>`: Pause all machines or a specific one.
- `vagrant resume` or `vagrant resume <machine-name>`: Resume all machines or a specific one.
- `vagrant halt` or `vagrant halt <machine-name>`: Shutdown all machines or a specific one.
- `vagrant destroy` or `vagrant destroy <machine-name>`: Destroy all machines or a specific one.
- `vagrant snapshot <sub-command> <machine-name>`: This one doesn't work without a machine name, although it would be nice if I could snapshot all machines at once.

Check out the [official documentation](https://www.vagrantup.com/docs/multi-machine/) for more info.