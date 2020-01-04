---
layout: post
title:  "Introduction to Ansible"
number: 2
date:   2016-08-08 3:00
categories: automation
---
If you're reading this, then at some point in your life you've had to manage a server, and your ingenious bash scripts snowballed into a framework/tool chain only you can understand. You pride yourself on your framework/tool chain, full of witty anecdotes and H2G2 references. It's your baby. It's your mark upon the world. But it sucks and you know it. Not on the face of it, but somewhere deep down, in the shadows of- you get the point.

Ansible is a deployment and orchestration tool. Say you need to set up a machine: you need to install Apache and copy some files to a directory. In bash you would have had to write the commands to do this, basically saying how you want things done. In Ansible, you can write tasks for the above work, telling it what you want done, and let Ansible take care of everything else. You can define the final state of your target system and let Ansible figure out the internals. This takes out a lot of the complexity of writing scripts, since you can really on pre-defined, well-tested and robust tasks to get the job done. The Ansible command for installing Apache would look something like this.

`apt: name="apache2" state="present"`

The above command will use the apt-get utility to install the apache2 package if it is not already installed. It is much easier to read, and someone new to your deployment team might get to know at first glance what is going on. The goal in Ansible is to define all your infrastructure tasks in terms of easy to read commands, which define the target state of your system instead of going into details and programming how your system gets to that state.

The word orchestration means what it sounds like. Imagine an orchestra, and a conductor, creating music from different instruments. Ansible will take your infrastructure instruments, like storage, computing, networking, etc., and use them to create your symphony.

## Installing Ansible
The Ansible website can tell you about installing Ansible on your control machine. The control machine is the one you will be using to deploy your environment. The commands for Linux look like this:

```
sudo apt-get install software-properties-common
sudo apt-add-repository ppa:ansible/ansible
sudo apt-get update
sudo apt-get install ansible
```

## How It Works
In order to work, Ansible uploads its modules over SSH to the target machine, executes them, and then deletes them. This makes Ansible agent-less, which means you don't need to install special software on your target machines in order for it to work.

## Trying It Out
Let's give Ansible a go. Just follow me:

- Set up a Vagrant machine with IP 192.168.1.20.
- Create a file called `hosts` in your current directory, and place the following in it:
- ```yaml
[myhost]
192.168.1.20     ansible_ssh_user=vagrant     ansible_ssh_password
```

- Run `ansible -i hosts myhost -m "ping"`
- Output should be
```
192.168.1.20 | SUCCESS => {
"changed": false,
"ping": "pong"
}
```

And that's it!

With Ansible you can create entire playbooks which define the tasks you want to run on your target machine. Ansible is very powerful, and provides plenty of modules for pretty much anything you can dream of. You can manage servers, modify configurations, manage AWS, Docker, etc. And since it’s been around for a good long while, you can find help on StackOverflow and other websites.

## Key Points

- Agent-less: There is no need to install special software on target systems.
- Declarative: Describe your target system and let Ansible figure out the details.
- Fewer moving parts: Since everything executes over SSH, which is tried and tested already, there is no additional technology involved.
- English instead of code: You no longer need to write complex code to get things done.
- Modules for everything: So you want to manage your infrastructure which uses technologies and cloud services from everybody? Among Ansible's hundreds of modules you'll probably find the one that you need, regardless of your technology vendor.
- No more managing the management: Since its clean, simple, and fast, you can run your playbooks and walk away.

The whole point of Ansible is to look at it and its modules like minions from the game Overlord. Select a task and let your minions run wild. You can have different types of minions for different types of jobs, but ultimately the goal is to exercise your untamed will upon the unsuspecting universe/infrastructure. So there.

One thing that really helped me move forward with Ansible was changing my mind-set. Instead of writing code to describe how to get things done, I could just describe my target system in simple English and let Ansible take care of the rest. Thinking in terms of system states was really helpful in understanding and using Ansible.

There are a lot of resources on Ansible, so be sure to check them out. The Ansible website has a lot of case studies and white papers for you to learn more.

## Resources
- [https://www.ansible.com/](https://www.ansible.com/)
- [https://www.ansible.com/case-studies](https://www.ansible.com/case-studies)
- [http://stackoverflow.com/questions/tagged/ansible](http://stackoverflow.com/questions/tagged/ansible)
- [https://groups.google.com/forum/#!forum/ansible-project](https://groups.google.com/forum/#!forum/ansible-project)