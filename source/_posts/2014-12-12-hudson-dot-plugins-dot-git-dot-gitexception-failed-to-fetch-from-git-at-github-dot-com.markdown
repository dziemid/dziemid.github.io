---
layout: post
title: "hudson.plugins.git.GitException: Failed to fetch from git@github.com"
date: 2014-12-12 21:49:26 +1100
comments: true
categories: jenkins github
---

## Problem: Red build

Unless your Jenkins project just stopped building with this exception `hudson.plugins.git.GitException: Failed to fetch from git@github.com` you are unlikely to get much of this post ;)

I guess there might be bunch of the reasons why you could see this message, but here is what happend to me..

<!--more--> 

My project hosted on github suddenly started to fail when building in jenkins. Initially I had no clue why and I've done bunch of google searches:

```
 jenkins github returned status code 128:
 jenkins ssh keys github stoped working
 "hudson.plugins.git.GitException: Failed to fetch from git@github.com"
```

## Investigation

Thinking a little bit more about my situation. We have a Jenkins that is connecting to GitHub using different ssh keys depending on project ([deploy key approach](https://developer.github.com/guides/managing-deploy-keys/#deploy-keys) ). So I continued to google:

```
 jenkins multiple ssh keys not working
 ssh debug which key git is using
``` 

That led me to additional debugging commands: `ssh -vvvT git@github.com` and adding in `~/.ssh/config`

```
  Host github.com 
    LogLevel DEBUG3
```

While checking out projects from github and looking and debugging output problem became quite clear. Git, while using SSH, was authenticating using incorrect ssh key.

## Solution

In the end, what happend was that previous day I've replaced `~/.ssh/config` on Jenkins box. Among other things, there was a problematic bit of config:

```
Host *
  ControlMaster auto
  ControlPath /tmp/ssh_mux_%h_%p_%r
  ControlPersist 10m   # - this was causing issues
```

> ControlPersist -  that automatically
>   starts a background ssh(1) multiplex master when connecting. This
>   connection can **stay alive indefinitely**, or can be set to
>   automatically close after a user-specified duration of inactivity

Above configuration was causing issues, when different projects were being build on Jenkins that were meant to use different ssh keys (while talking to github). 

Removing above config solved the problem.