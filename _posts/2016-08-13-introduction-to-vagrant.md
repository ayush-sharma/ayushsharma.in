---
layout: post
title:  "Introduction to Vagrant"
number: 6
date:   2016-08-13 1:00
categories: development
---
I've been using Vagrant for quite a while now. It's not just where I do my dev testing, it’s also where I do all my experiments with new tools and technologies. Vagrant is great for doing crazy things without actually breaking your system, and if you're not already using it, you should start now.

Vagrant is essentially a command-line interface to virtual machines that you can install on your own system. If you're already using VirtualBox or VMware, then understanding Vagrant will not take long.

Imagine this scenario. You start your virtual machine, which is already like your production system, test your code, and destroy the machine if you want to. Then just start it again and repeat. The entire process is reproducible. You can do this as many times as you want. Vagrant allows you to create disposable virtual machines for, to start with, setting up a development environment. You can easily SSH into the machine, set up the softwares you want, test your code, and destroy the machine when you're done. And all this with a few simple commands. It's really that simple.

You can specify everything in a single configuration file, check it into your version control system, share the file with your fellow developers, and they can use the file to set up the exact same machine. No more "works on my machine" excuses.

## Getting Started

- Install Vagrant on your system.
- Create a new directory to experiment with a new Vagrant setup.
- Go into the new directory and initialise Vagrant:
`vagrant init hashicorp/precise64`
- Do `vagrant up` to bring the machine online.
- Do `vagrant ssh` to SSH into the machine.

If you're now at a new vagrant prompt, then that's it! You've successfully deployed a new Vagrant machine on your system with just a few simple commands.
If you notice in your directory, there is a new `Vagrantfile` there. This is the file that contains all the details of your new machine. It will mostly contain a lot of commented out lines, but right at the top you should see this:

`config.vm.box = "hashicorp/precise64"`

This line indicates the VM image for the current box. When you run the vagrant up command, Vagrant will bring up a new machine with this image. This file, committed into source code, will make sure that other developers will have the same machine.

## How Vagrant Creates VMs
Vagrant uses something called "base boxes" to bring new VMs online. If you see above, `hashicorp/precise64` is a base box that Vagrant downloads from the HashiCorp Atlas catalogue. If you do not provide a full URL for your base box, Vagrant will look for it there. To create the actual VM, Vagrant plugs into existing VM software like VirtualBox or VMware to create virtual machines. So you will need to have that installed on your system. After you do `vagrant up`, in less than a minute a new machine will be brought online.

## Shared Folders
Vagrant will automatically put a shared folder under `/vagrant` on the new machine so you can access your project files. This way you can modify your project files in your host environment, and Vagrant will keep the directories in sync. This is great for hosting, say, a website inside the Vagrant box by making /vagrant your Apache document root. You can make modifications in the host using your favourite text editor, and have the Apache in the box serve your new changes.

## Vagrant Commands
There are several Vagrant commands which you can use to control your machine. Some of the important ones are:

- `vagrant up`
Bring a machine online.
- `vagrant status`
Show current machine status.
- `vagrant suspend`
Pause the current machine.
- `vagrant resume`
Resume the current machine.
- `vagrant halt`
Shutdown the current machine.
- `vagrant destroy`
Destroy the current machine. By running this command, you will lose any data stored on the machine.
- `vagrant snapshot`
Take a snapshot of the current machine.

## Some History
Vagrant was started in January 2010 by Mitchell Hashimoto. In November 2012, HashiCorp was formed by Mitchell to back the development of Vagrant full-time. HashiCorp builds commercial additions and provides professional support and training for Vagrant.

## Resources
- [https://www.vagrantup.com/](https://www.vagrantup.com/)
- [https://en.wikipedia.org/wiki/Vagrant_(software)](https://en.wikipedia.org/wiki/Vagrant_(software))
- [http://www.vagrantbox.es/](http://www.vagrantbox.es/)
- [https://github.com/mitchellh/vagrant-aws](https://github.com/mitchellh/vagrant-aws)