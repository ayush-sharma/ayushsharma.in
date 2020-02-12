---
layout: post
title:  "Deploying git submodules in Bitbucket Pipelines"
number: 86
date:   2020-02-10 00:00
categories: automation containers
---
Bitbucket Pipelines is a great deployment tool tightly integrated into Bitbucket. It allows you to trigger deployments directly from repository branches and tags, and you can customize your deployment steps as needed.

One missing feature in Pipelines is that it is not able to automatically clone submodules that are added to your repository as this requires some additional settings. Until this feature gets added by default, there is a simple way to achieve this.

First, we need to make sure our repository has access to clone the sub-modules repository. You can configure this by reading <this>.

Next, the `git submodule update --init --recursive` can initialize, fetch and checkout any nested submodules. To configure the command in Pipelines, add it to the beginning of the `script` block.

```yaml
pipelines:
  default:
    - step:
        script:
          - git submodule update --init --recursive
```

The above Pipeline checks out all nested submodules during its execution. We can then use them in subsequent build steps.

<img src="{{ site.images-path | prepend: site.baseurl | prepend: site.url }}2020-02-12-deploying-git-submodules-in-bitbucket-pipelines.png" alt="Deploying git submodules in Bitbucket Pipelines">

# Resources
- [Git Tools - Submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules)