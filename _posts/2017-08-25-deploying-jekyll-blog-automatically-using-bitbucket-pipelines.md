---
layout: post
title:  "Deploying Jekyll blog automatically using Bitbucket Pipelines"
number: 50
date:   2017-08-25 1:00
categories: automation
---
So.

For my 50th note, I though I’d finally spend some time trying to figure out how to deploy this blog automatically instead of manually running an Ansible script every time. After an hour or so, I came up with a pretty simple solution.

Have you heard of Bitbucket Pipelines? It’s a pretty great CI/CD pipeline by Atlassian that’s integrated into Bitbucket. The idea is very simple. You just add a `bitbucket-pipelines.yml` file in the root of your git repo, place some deployment instructions in it, and Bitbucket will launch a new Docker container on the next `push` and run those instructions. These instructions could be pretty much anything, from running test cases to deploying code to remote servers, etc.

Pipelines has a very basic feature set, but they’re pretty useful. Here’s some of the basic stuff we’ll be using:
- It allows you to specify SSH keys and Known Hosts for the docker container Pipelines launches. Bitbucket will place these credentials in the new container, which makes doing stuff over SSH really easy. Let’s say you want Pipelines to deploy code to a remote server. You can create a new SSH key pair, place the public key on the remote host, and fetch the host address, which Pipelines will place in the Known Hosts file on the new Docker container it launches. It will also place the private key you generated within the new container.
- There is also a provision to define encrypted environment variables that you can use in the Pipelines file.

So let’s get started.

## Enabling Pipelines in Bitbucket
First, go to the `Settings` page of your repo in Bitbucket. The link should be somewhere on the left sidebar. Once there, find `Settings` under the `Pipelines` section in the sub-menu that opens up. On the new page, you’ll find just one button which says `Enable Pipelines`. Let’s activate that.

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}bitbucket-pipelines-enable-screen.jpg" width="700" height="500" alt="Enabling Bitbucket Pipelines for your repository.">

## Setting Environment Variables
Next, head to the `Environment variables` section in the same sub-menu. Here you can add key-value pairs that are going to be available for use in the Docker container using the `$` symbol. Let’s define a variable with the name `PRODUCTION_HOST` and the value of the domain/IP of the machine we want to deploy our code on. Make sure to check `Secure` to ensure the variable is encrypted. Hit `Add` when you’re done.

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}bitbucket-pipelines-environment-variables-screen.jpg" width="700" height="432" alt="Setting the environment variables for bitbucket-pipelines.yml file.">

## Configuring SSH Keys
Let’s head to the `SSH Keys` section. The link is in the same sub-menu. On this page we’ll do two things: create a new SSH key-pair to use for deployment, and add the host address and fingerprint of our target machine so we can deploy securely. The process is pretty straight-forward. You can use the interface to generate a new key-pair. Bitbucket will place the new private key in the container it launches for deployment, and you should place the public key on the remote machine. That way we’ll be able to securely establish an SSH connection between our Docker container and the remote machine without any additional configuration in the container itself. Also, add the host address of the target machine in the `Host address` section below. It will fetch the fingerprint of the target machine and load it in the `known_hosts` file in the container, to verify that the container knows the target machine. Note that the domain/IP should be the same as the one you defined in `PRODUCTION_HOST` variable above, since that’s the variable we’ll use to establish a connection.

## Creating the bitbucket-pipelines.yml file
Once the above configuration is done, go ahead and create a `bitbucket-pipelines.yml` file in the root of your git repo. Place the following content in it:

```yaml
image: jekyll/builder:latest

pipelines:
  default:
    - step:
        script:
          - jekyll build
          - rsync -a _site/ root@$PRODUCTION_HOST:/var/www/ --exclude=bitbucket-pipelines.yml --exclude=deploy --exclude=Vagrantfile --chown=www-data:www-data
```

Save the file and `commit`, but don’t `push` yet. Let’s go over the file before we do that.

- The `image:` section contains the image name of the Docker container you want Pipelines to launch. You can specify the name of any public container available on DockerHub. Pipelines will download and launch that container, and run the commands we specify in the rest of our file. We’re using the `jekyll/builder` container, since it already has everything that we’ll need to build and deploy our Jekyll blog.
- The `script:` section contains the actual commands we want Pipelines to run after the container launches. The commands here are pretty basic: we’re building our blog using `jekyll build`, and then we `rsync`-ing the `_site` directory, which is where Jekyll will place our built blog, to the target machine. Note that we’re using the `$PRODUCTION_HOST` variable, which Pipelines will populate in the container. I won’t go over the syntax for the `rsync` command, but remember to exclude files you don’t want and setting the right permissions on those files after upload.

Pipelines has syntax available for running different commands on the `push` of different branches. So depending on whether `staging`, `production`, or `feature_x` branch was `push`-ed, you could run different commands and deploy in different ways. Since we haven’t specified anything, the above file will run commands when we `push` any and all branches.

## Watching It Work
Once the above is done, `push` your branch to Bitbucket. Head to the `Pipelines` link of your repo in the sub-menu, and you should see it all in action. Once successfully deployed, you’ll see a page like this:

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}bitbucket-pipelines-successful-deployment-screen.jpg" width="700" height="267" alt="Watching the magic happen. A successful deployment using Bitbucket Pipelines.">

And that’s it! What we just did was automate the build and deploy process of our website. Pipelines has a lot of other interesting options, and can do a lot more than the simple task we accomplished. Please remember that Pipelines offers 50 minutes of build time on the free account, but even after that its not very expensive. Pipelines is a good way to get a CI/CD setup for free and have an easier life by letting Bitbucket take care of deployments for you. Pipelines will also send you emails when deployments fail.

I’m writing this note in Evernote right now. I’ll be `commit`-ing and `push`-ing it soon. If you’re reading this right now, it means it all went fine. For some reason I’m getting a message-in-a-bottle feeling right now. Fingers crossed 😃

## Resources
- [Bitbucket Pipelines general info](https://bitbucket.org/product/features/pipelines).
- [Pipelines configuration options](https://confluence.atlassian.com/bitbucket/configure-bitbucket-pipelines-yml-792298910.html).