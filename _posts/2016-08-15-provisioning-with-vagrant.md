---
layout: post
title:  "Provisioning with Vagrant"
number: 11
date:   2016-08-15 1:00
categories: automation
---
If you've been through the [Introduction to Vagrant]({% post_url 2016-08-13-introduction-to-vagrant %}) notes, then you have a basic understanding of what Vagrant really is. But you're probably wondering how to get more out of it. I mentioned earlier that you can put the commands you run to set up your machine in the `Vagrantfile`, and then check it into source control. That way, anyone who uses that same file will have the same environment. No more "works on my machine". We're going to do that part of it today with Provisioning.

Depending on what softwares you need, you can always find a ready-to-go box on the [HashiCorp Atlas catalogue](https://atlas.hashicorp.com/boxes/search). Failing that, you can SSH into the machine and install the softwares by hand. But what if we want a more repeatable, automated process?

We are going to discuss two types of provisioning today, `shell` provisioning and `Ansible` provisioning.

## Shell Provisioning
Shell provisioning works by supplying the shell commands that you need to set up your machine to Vagrant. You can define your commands `inline`, or provide a `path` to the shell file instead. An inline command would look like this:

```ruby
Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/trusty64"

   config.vm.provision "shell", inline: <<-SHELL
     sudo apt-get install -y apache2
     sudo apt-get install -y php5 libapache2-mod-php5
   SHELL
end
```

As you can see, when you run the `vagrant up` command, it will run your shell provisioner and execute all the commands that you list. Once this file is in source control, the same commands will run for anyone who brings up their machine. You can check out this section on the [official documentation](https://www.vagrantup.com/docs/provisioning/shell.html) for more details.

## Ansible Provisioning
In Ansible provisioning, instead of providing shell commands, you provide an Ansible script instead. Create a file called `ansible.yml` and place the following content in it:

```yaml
---
- hosts: all
  become: true
  tasks:
  - name: Install things
    apt: name={{ item }} state=present
    with_items:
    - apache2
    - php5
    - libapache2-mod-php5
```
Then provide this file to Vagrant:

```ruby
Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/trusty64"

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "ansible.yml"
  end
end
```

Let's destroy the old Vagrant VM before we do this test. Run `vagrant destroy`, and then run `vagrant up`. You should see the Anisble provisioner run our new Ansible file.

The provisioning described above would kick in under a few conditions:

- The first `vagrant up`.
- `vagrant provision`.
- `vagrant reload --provision`.

If you want to bring up your machine without running provisioning, you could use the `--no-provision` flag.