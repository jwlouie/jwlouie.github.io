---
layout: post
title: "Deploying using Chalk and Jekyll"
description: "Guide for myself not to forget how to develop/develop my own website"
tags: [jekyll, docker, github-pages]
---

So first off, I should make sure that I don't forget how to maintain my 
own website by documenting the steps I took to get this first version up 
and running.

**DISCLAIMER**: This post in no way represents "best practices". These are
simply notes for myself to reference (and iterate/improve on as I learn more).

## Deployment Environment

While I didn't have to, I wanted to set up my development environment within
a Docker container. I wanted to do so for a couple reasons:

1. I wanted to gain more experience using Docker (since I'm very much
   still a Docker newbie).
2. I wanted to ensure that my development environment would be portable/platform
   agnostic.
   1. I didn't want dependencies specific to creating my website to affect my
      default system packages (e.g. if I need to install Python3 I don't want
      installing a specific Python3 version to affect my personal/work 
      machines).
3. I wanted to ensure that my development environment was as "lightweight" and
   "ephemeral" as possible. This way I could be like, "Hey, let me write up/
   modify a website post" and I could just spin up the Docker container.

I will be adding the Dockerfile itself to this repo as well as making further 
refinements to make the development easier (e.g. add a volume to help persist
container data, etc.). I do however, want to note a couple learnings that I 
found along the way (no matter how basic they were):

- You can determine the containers network settings at startup using `--network`
  (e.g. `--network host`). Using the `host` setting makes things much easier 
  (though I can't speak to security) because the container's network settings
  will mirror that of the host machine's and you can easily access interfaces
  like `localhost`.
- Every time you create a new interactive session (using only RAM/memory and not
  Docker volumes), you create a new, distinct running container (e.g. changes to
  files in one shell may not manifest themselves in another shell).

## Using My GitHub Repository

Because the theme I'm using, Chalk, doesn't seem to support deploying code 
directly to the `master` branch (perhaps I'll look into why that is and whether 
that can be changed), I figured I would need a couple branches. Here is how I
intend to organize my development:

* `dev`: This branch will contain all of the code/posts that I need to use/write.
  I have set this branch as the default branch in GitHub, this way any changes
  that I make will push to this branch automatically (rather than `master`, which
  is normally how repos are set up). 
* `gh-pages`: This is the default repository branch where the Chalk theme's publishes
  "build"/rendered code. In some senses I'm using this as a sort of "staging"
  branch where I stage built code from the `dev` branch and then push it to `master`
* `master`: This Github Pages reads from by default. Thus, in order to get `master`
  up to date with the `gh-pages` branch, we can simply rebase `master` on the 
  `gh-pages` branch (see below for commands).

## Making Changes and Deploying

Let's cover an end-to-end use case of my intended workflow (assuming Docker is
installed):

### Build Docker Image

{% highlight bash %}
$ cd <DIR_WITH_DOCKERFILE>
$ docker build --tag=<TAG_NAME> .
{% endhighlight %}

### Start Docker Container

{% highlight bash %}
$ docker run --network host -it <TAG_NAME> # Default user is root; definitely needs to be changed
$ npm run local
{% endhighlight %}

### Commit/Push Changes to `dev` Branch

{% highlight bash %}
$ git pull
# Make appropriate changes
$ git add <MODIFIED_FILES>
$ git commit -m "<COMMIT_MESSAGE>"
$ git push origin dev
{% endhighlight %}

### Stage Changes to `gh-pages`

{% highlight bash %}
$ npm run publish # Creation and updating of gh-pages automatically handled by theme's code
{% endhighlight %}

### Push to `master` (Prod)

{% highlight bash %}
$ git checkout master
$ git branch -u gh-pages
$ git rebase gh-pages
$ git push origin master
{% endhighlight %}

