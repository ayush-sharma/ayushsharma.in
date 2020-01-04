---
layout: post
title:  "Dockerizing Tweet-Toot: A practical guide to deploying your app using Docker"
number: 74
date:   2018-09-26 0:00
categories: automation
---
In [Tweet-Toot: Building a bot for Mastodon using Python]({% post_url 2018-09-06-tweet-toot-building-a-bot-for-mastodon-using-python %}), I wrote a guide to building a Twitter relay in Python. The app works as a cron job which would watch a Twitter account and repost any new tweets to a Mastodon account. The source code of the app is [available on GitHub](https://github.com/ayush-sharma/tweet-toot).

In this article,I'm going to guide you through setting up Tweet-Toot in a Docker container. But before we can do that, we need to review the steps needed to install Tweet-Toot manually.

To install and use Tweet-Toot:

1. Clone the [Tweet-Toot GitHub repo](https://github.com/ayush-sharma/tweet-toot).
2. Install the Python3 libraries `requests` and `beautifulsoup` mentioned in the `requirements.txt` file.
3. In `config.json`, update the following:

- `tweets.source_account_url`: The source Twitter account.
- `toots.host_instance`: The HTTPS URL of your instance.
- `toots.app_secure_token`: The Mastodon app access token.

For example:

- `tweets.source_account_url` = https://twitter.com/SarcasmMother
- `toots.host_instance` = https://botsin.space
- `toots.app_secure_token` = XXXXX-XXXXX-XXXXX-XXXXX-XXXXX'

Once it's all setup, just run the main file like this:

`python3 run.py`

If all goes well, you'll see something like this:
```bash
Tweet-Toot | 2018-09-06 22:59:10 _info > getTweets() => Fetched tweets for https://twitter.com/SarcasmMother.
Tweet-Toot | 2018-09-06 22:59:10 _info > __main__ => 20 tweets fetched.
Tweet-Toot | 2018-09-06 22:59:10 _info > tootTheTweet() => Tweet 1031642593594028032 was already posted. Skipping...
Tweet-Toot | 2018-09-06 22:59:10 _info > tootTheTweet() => Tweet 1031640753187958786 was already posted. Skipping...
Tweet-Toot | 2018-09-06 22:59:10 _info > tootTheTweet() => Tweet 1031632691500789761 was already posted. Skipping...
Tweet-Toot | 2018-09-06 22:59:10 _info > tootTheTweet() => New tweet 1031572182114004993 => "You discovered the ability to time travel. You go 30 years into the future expecting to meet your future self only to discover that you've been missing for 30 years.".
Tweet-Toot | 2018-09-06 22:59:11 _info > tootTheTweet() => OK. Posted tweet 1031572182114004993to Mastodon.
Tweet-Toot | 2018-09-06 22:59:11 _info > tootTheTweet() => Response: {"id":"100680004506399841","created_at":"2018-09-06T17:29:11.674Z","in_reply_to_id":null,"in_reply_to_account_id":null,"sensitive":false,"spoiler_text":"","visibility":"public","language":"en","uri":"https://botsin.space/users/motherofsarcasm/statuses/100680004506399841","content":"\u003cp\u003eYou discovered the ability to time travel. You go 30 years into the future expecting to meet your future self only to discover that you\u0026apos;ve been missing for 30 years.\u003c/p\u003e","url":"https://botsin.space/@motherofsarcasm/100680004506399841","replies_count":0,"reblogs_count":0,"favourites_count":0,"favourited":false,"reblogged":false,"muted":false,"pinned":false,"reblog":null,"application":{"name":"TweetToot","website":""},"account":{"id":"-----","username":"motherofsarcasm","acct":"motherofsarcasm","display_name":"Mother Of Sarcasm","locked":false,"bot":true,"created_at":"2018-08-20T15:07:42.747Z","note":"\u003cp\u003eFOLLOWS YOU\u003c/p\u003e","url":"https://botsin.space/@motherofsarcasm","avatar":"https://files.botsin.space/accounts/avatars/000/058/348/original/658f78e1f07e94fa.jpg","avatar_static":"https://files.botsin.space/accounts/avatars/000/058/348/original/658f78e1f07e94fa.jpg","header":"https://botsin.space/headers/original/missing.png","header_static":"https://botsin.space/headers/original/missing.png","followers_count":0,"following_count":1,"statuses_count":7,"emojis":[],"fields":[{"name":"Name","value":"Mother Of Sarcasm"},{"name":"Owner","value":"ayushsharma22@mastodon.technology"},{"name":"Twitter Relay","value":"\u003ca href=\"https://twitter.com/SarcasmMother\" rel=\"me nofollow noopener\" target=\"_blank\"\u003e\u003cspan class=\"invisible\"\u003ehttps://\u003c/span\u003e\u003cspan class=\"\"\u003etwitter.com/SarcasmMother\u003c/span\u003e\u003cspan class=\"invisible\"\u003e\u003c/span\u003e\u003c/a\u003e"}]},"media_attachments":[],"mentions":[],"tags":[],"emojis":[]}
Tweet-Toot | 2018-09-06 22:59:11 _info > __main__ => Tooted "You discovered the ability to time travel. You go 30 years into the future expecting to meet your future self only to discover that you've been missing for 30 years."
Tweet-Toot | 2018-09-06 22:59:11 _info > __main__ => Tooting less is tooting more. Sleeping...
```

With the above setup working, we now have a small Python script that will watch a Twitter account and repost new tweets to our configured Mastodon account. But the installation steps are a bit tedious... wouldn't it be nice if we could package everything, including the Python 3 library requirements that we need, in one container?

## What is Docker?
Docker is small and portable package that can contain our application code and dependencies which we can then deploy as-is on any platform. Think of it like a smaller virtual machine: we can install our application, libraries, and configuration changes, and package it all in a *container* which can then run anywhere. Docker containers can be run on a local development machine or in production, and we can rest assured that they are exactly the same. The philosophy behind Docker is the same as the one behind [Vagrant]({% post_url 2016-08-13-introduction-to-vagrant %}), so if you've worked with Vagrant before, working with Docker will come naturally. Docker is at a basic level a more production-ready form of Vagrant.

The architecture of a Docker container is similar to that of a virtual machine, but with one important difference. While the virtual machine contains a hypervisor and guest operation system...

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}dockerizing-tweet-toot-a-practical-guide-to-deploying-your-app-using-docker-vm-architecture.png" width="441" height="221" alt="Architecture of a virtual machine.">

...  a Docker container architecture contains only a Docker engine.

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}dockerizing-tweet-toot-a-practical-guide-to-deploying-your-app-using-docker-docker-architecture.png" width="441" height="281" alt="Architecture of a Docker container.">

This provides improved levels of performance and portability. Once we create a Docker container for our application, we can deploy that container virtually anywhere. This is our goal in this guide: to create a Docker container for Tweet-Toot.

## Installing Docker
The steps to install the Docker Engine - Community Edition are [available here](https://store.docker.com/search?type=edition&offering=community). On Debian, the following commands are needed:

```bash
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -

sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/debian \
   $(lsb_release -cs) \
   stable"

sudo apt-get update

sudo apt-get install docker-ce
```

With the installation complete, we can verify it using:


```bash
docker --version

Docker version 18.06.1-ce, build e68fc7a
```

Another basic test for Docker would be to run the `hello-world` container. We can do this by running the command below:


```bash
docker run hello-world
```

Running the above command should show us the following:

```bash
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
d1725b59e92d: Pull complete
Digest: sha256:0add3ace90ecb4adbf7777e9aacf18357296e799f81cabc9fde470971e499788
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

This is what is happening above:

- Docker does not find the library/hello-world container on the local machine, so it will `pull` it from the Docker Hub repository.
- Once downloaded, it will run the instructions within the container, which is to display the help message you see above.

## Creating a Docker Hub account

We are also going to need a Docker repository. Docker repositories work the same way as `git` repositories, the difference being that `git` repos maintain code while Docker repos maintain Docker images. [Docker Hub](https://hub.docker.com/) is a free Docker repository we can use. Once you sign-up for an account, create a new repository for this project, and keep your user ID, passsword, and repository name handy. Your user ID will be on the top-left of your Docker Hub account.

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}2018-09-26-dockerizing-tweet-toot-a-practical-guide-to-deploying-your-app-using-docker-docker-hub-account.png" width="1273" height="397" alt="Your Docker Hub user ID is on the top left of your account.">

Once your account is ready, use the following command...

```bash
docker login
```

... and log-in with your user Hub ID and password. The `docker login` process will ask you for your Docker user ID and password, and conce setup, we will be able to `push` our Docker buils to this repo, just like we would `push` our `git` code to a repostiry.

## Building the Docker container
With Docker installed, we can begin creating a container for Tweet-Toot.

Docker containers are created by specifying the steps needed in a `Dockerfile`. For Tweet-Toot, the installation steps will be added to its own `Dockerfile`, which will then be *built* to create our new Docker container.

Let's start by navigating to the directory where we have cloned Tweet-Toot and creating a new `Dockerfile`.

The first thing to go in our new `Dockerfile` will be the base container. Every Docker container is built, or *layered*, on top of another container. Any new changes to a container are added as a file-system layer than can be add incrementally to our build without changing the layers that have not changed. This allows for a much smaller and portable Docker container. For Tweet-Toot, we're going to create our new container on top of the base *Ubuntu* container.

Let's add the following lines to our new `Dockerfile`:

```bash
# Docker image for Tweet-Toot project.
FROM ubuntu
MAINTAINER <email@address.com>
```

The `FROM` line indicates our base *Ubuntu* container. The `MAINTAINER` line specified the email address of the project maintainer.

Next, we want to specify our Mastodon application token as an argument which we will provide during the build process, since we don't want to hard-code this in our `Dockerfile`. So, let's add the following line:

```bash
ARG mastodon_token
```

Next, in our Docker container, we want to clone the repo, install the dependencies, and configure our Mastodon token in the `config.json` file. So let's add the commands below to our `Dockerfile`:

```bash
RUN cd /root;\
    apt-get -y update;\
    apt-get -y upgrade;\
    apt-get -y install python3 python3-pip git wget cron;\
    git clone https://github.com/ayush-sharma/tweet-toot.git;\
    cd tweet-toot;\
    pip3 install -r requirements.txt;\
    apt-get -y purge python3-pip git;\
    apt-get -y install python3-idna;\
    apt-get -y autoremove;\
    apt-get -y autoclean;\
    # Configure Tweet-Toot
    sed -i 's/"toots.app_secure_token": ""/"toots.app_secure_token": "'$mastodon_token'"/g' config.json
```

The `RUN` command will execute shell commands inside our container during the build process. Notice that we're running the above command as if we were installing Tweet-Toot on a new Ubuntu machine. This is exactly what Docker containers are for, to package our dependencies as standalone entities which can be deployed anywhere. By packaging everything in a single container from scratch, we can atomically deploy our container anywhere and achieve an immutable and works-everywhere infrastructure.

Next we will configure Tweet-Toot as a cron job, within the container itself. This way whenever our container executes, our cron jobs will be triggered as well. So let's add the lines below to our Docker container:

```bash
RUN crontab -l > /tmp/crontab;\
    echo '* * * * * cd /root/tweet-toot; python3 /root/tweet-toot/run.py >> /tmp/tweet-toot.log' >> /tmp/crontab;\
    crontab /tmp/crontab
```

We also want to create the log file that the above cron job will write logs to. Let's add the following line to our `Dockerfile`:

```bash
RUN touch /tmp/tweet-toot.log
```

Lastly, we want to execute our cron job, and `tail` the log file so that the container has something to do, otherwise it will execute once and shutdown. Let's add the following line to our `Dockerfile`:

```bash
CMD cron && tail -f /tmp/tweet-toot.log
```

The above command will run our cron job and begin tailing our log file `/tmp/tweet-toot.log`. With this continuous `tail` process running, our container will continue to run and not shutdown automatically.

With the last line in place, our entire `Dockerfile` should now look like this:

```bash
# Docker image for Tweet-Toot project.
FROM ubuntu
MAINTAINER <email@address.com>

ARG mastodon_token

RUN cd /root;\
    apt-get -y update;\
    apt-get -y upgrade;\
    apt-get -y install python3 python3-pip git wget cron;\
    git clone https://github.com/ayush-sharma/tweet-toot.git;\
    cd tweet-toot;\
    pip3 install -r requirements.txt;\
    apt-get -y purge python3-pip git;\
    apt-get -y install python3-idna;\
    apt-get -y autoremove;\
    apt-get -y autoclean;\
    # Configure Tweet-Toot
    sed -i 's/"toots.app_secure_token": ""/"toots.app_secure_token": "'$mastodon_token'"/g' config.json

RUN crontab -l > /tmp/crontab;\
    echo '* * * * * cd /root/tweet-toot; python3 /root/tweet-toot/run.py >> /tmp/tweet-toot.log' >> /tmp/crontab;\
    crontab /tmp/crontab

CMD cron && tail -f /tmp/tweet-toot.log
```

We can now build our new container now that our `Dockerfile` is complete. Execute the build process by running the command below:

```bash
docker build --build-arg mastodon_token=123 -t <repo>:<tag> .
```

... where:

- `<token>` is your Mastodon application token.
- `<repo>` is your DockerHub repository.
- `<tag>` is the repository tag you want to create.

For example:

```bash
docker build --build-arg mastodon_token=123 -t ayushsharma22/tweet-toot:0.5 .
```

<video width="730" height="518" preload="auto" muted controls="controls">
  <source src="{{ site.videos-path | prepend: site.baseurl | prepend: site.url }}2018-09-26-dockerizing-tweet-toot-a-practical-guide-to-deploying-your-app-using-docker-docker-build-process.webm" type="video/webm">
Your browser does not support this video.
</video> 

Running the above will execute all the steps we've provided in our `Dockerfile`. On successful execution, we should see the following output:

```bash
Successfully built <random_hash>
Successfully tagged <repo>:<tag>
```

Yes! We've just built our first Docker container!

## Pushing the Docker container to Dockerhub
With our container built, it's time to `push` it to our DockerHub container repository, much like we would push code to a `git` repository.

Run the following command:

```bash
docker push <repo>:<tag>
```

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}2018-09-26-dockerizing-tweet-toot-a-practical-guide-to-deploying-your-app-using-docker-docker-push-production.png" width="864" height="518" alt="Pushing our Docker container to Docker Hub.">

The above command should push our new Docker container to our DockerHub account. Once this is done, head over to your DockerHub repository, and under the `Tags` section, you should see the new tag.

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}2018-09-26-dockerizing-tweet-toot-a-practical-guide-to-deploying-your-app-using-docker-docker-hub-tags.png" width="1261" height="317" alt="Seeing our new Docker tag on Docker Hub.">

## Running Tweet-Toot using the Docker container
We've successfully pushed our new container to the DockerHub repository. We can now run it in one of two modes: **interactive mode** and **daemon mode**. Interactive mode will allow us to enter our Docker container while it runs and execute commands, and daemon mode will run it in the background.

Let's run our Docker container in daemon mode. Execute the following command:

```bash
sudo docker run -d <repo>:<tag>
```

The above command will run our container in the background in daemon mode. Let's first see if our container is running:

```bash
docker ps

CONTAINER ID        IMAGE                          COMMAND                  CREATED             STATUS              PORTS               NAMES
3c5e68cc6bea        ayushsharma22/tweet-toot:0.5   "/bin/sh -c 'cron &&…"   2 minutes ago       Up 2 minutes                            quizzical_shockley
```

Now let's log-in to our running container:

```bash
docker exec -it 3c5e68cc6bea bash
```

This will connect us to our Docker container and drop us to a `bash` shell prompt. If we navigate to the log file we configuerd, `/tmp/tweet-toot.log`, we should see the output of our Python script.


## Deploying the Tweet-Toot Docker container
With out container built, we're ready to deploy it to production. Log-in to your production server, and run the commands below:

```bash
docker pull <repo>:<tag>
```

The above command will pull our recently built container from the Docker Hub repo, just like we would `git pull` to get the latest code.

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}2018-09-26-dockerizing-tweet-toot-a-practical-guide-to-deploying-your-app-using-docker-docker-pull-production.png" width="863" height="518" alt="Pull our new Docker commit on our production server.">


Once `pull`ed, we can run it in daemon mode just like before:

```bash
docker run -d <repo>:<tag>
```

And that's it :)

We now have our Docker container running in production, the same way we did on our development environment.

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}2018-09-26-dockerizing-tweet-toot-a-practical-guide-to-deploying-your-app-using-docker-docker-run-production.png" width="863" height="518" alt="Running our Docker container in production.">

We can verify if our container is running in production using the `docker ps` command:

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}2018-09-26-dockerizing-tweet-toot-a-practical-guide-to-deploying-your-app-using-docker-docker-ps-production.png" width="863" height="518" alt="Checking if our Docker container is running in production.">

## Conclusion
Docker gives us a way to pre-package our application code and dependencies, and deploy this package, or *container*, immutably to our production servers. This gives us the ability to ensure that the environment on our system and producttion servers is exactly the same, and also keeps our application isolated and portable inside its own package. This is a really powerful feature than can be leveraged to build great software.

## Resources

- [Docker tutorial on Tutorials Point](https://www.tutorialspoint.com/docker/)
- [Docker Hub](https://hub.docker.com/).