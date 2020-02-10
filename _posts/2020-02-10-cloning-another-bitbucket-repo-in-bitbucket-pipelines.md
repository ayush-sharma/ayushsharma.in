---
layout: post
title:  "Cloning another Bitbucket repository in Bitbucket Pipelines"
number: 85
date:   2020-02-10 00:00
categories: automation containers
---
# Cloning another Bitbucket repo in Bitbucket Pipelines

I recently had a use-case where I wanted to clone another Bitbucket repository during a Pipelines execution. Doing this is very simple, but there is a lot of conflicting information online, so I thought I would document the steps here.

Imagine a very simple Pipeline that looks like this:

```bash
pipelines:
  default:
    - step:
        script:
          - git clone git@bitbucket.org:ayushsharma/my-submodules.git
```

The repository that triggers the Pipeline will need permission to clone `my-submodules`. Doing so requires adding the public SSH key to `my-submodules`.

## Create SSH keys for the main repository
In Bitbucket, go to the repository SSH keys page under `Settings > Pipelines > SSH keys`.

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}cloning-another-bitbucket-repo-1.png" alt="Cloning another Bitbucket repository in Bitbucket Pipelines - Settings > Pipelines > SSH keys">

Next, click on `Generate keys` to let Bitbucket auto-generate a random, secure SSH key-pair. You can also upload a custom key-pair if you want.

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}cloning-another-bitbucket-repo-2.png" alt="Cloning another Bitbucket repository in Bitbucket Pipelines - Generate keys">

Once completed, click `Copy public key`. We will need to paste this in the next step.

## Add SSH public key in the target repository
Go to the `my-submodules` repository. Under `Settings > General > Access keys`, you should see the option to add SSH public keys to gain read-only access.

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}cloning-another-bitbucket-repo-3.png" alt="Cloning another Bitbucket repository in Bitbucket Pipelines - Settings > General > Access keys">

Click `Add key`, enter a label, and paste the public key we copied in the previous step.

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}cloning-another-bitbucket-repo-4.png" alt="Cloning another Bitbucket repository in Bitbucket Pipelines - Add key">

Our main repository now has read-only permissions to clone `my-submodules` from within its Pipelines. After the above configuration, executing a build for the repository will show all-green.

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}cloning-another-bitbucket-repo-5.png" alt="Cloning another Bitbucket repository in Bitbucket Pipelines - Successful deployment!">

## Resources

- [Use SSH keys in Bitbucket Pipelines](https://confluence.atlassian.com/bitbucket/use-ssh-keys-in-bitbucket-pipelines-847452940.html).