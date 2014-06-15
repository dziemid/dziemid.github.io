---
layout: post
title: "Analysing AWS CloudTrail logs with ElasticSearch and Kibana"
date: 2014-06-15 18:33:12 +1000
comments: true
categories: aws
---

Summary of my recent spike with ElasticSearch, Kibana and Docker.

## Quick and dirty forensics

Recently we had an situation were [CloudTrail](http://aws.amazon.com/cloudtrail/) was invaluable tool in finding out what happened. The only issue was usability of the logs.

We are new to the tool, so at the time we had a logging enabled, but not much more. When the incident happened we pretty much just run ```s3sync sync``` and than worked with logs "by hand" in a manner similar to this:

```
cat * | jq . -C | grep -i eventname -A 10 -B 10 \
| grep -iv "describe" | grep -iv "list" | grep -iv "get" | less -R
```

It worked, but left me with a feeling that there must be a better way. 

## The better way

{% pullquote %}
Talking with colleague on the tram an idea emerged in my head. {"How hard would it be to get something useful out of ElasticSearch and Kibana?"} I never used either, but I've been told I should be pretty easy.

I am here to report, it was easy and fun.. taking less than 2h to get to interesting results. 
{% endpullquote %}

In the end, I had a great interactive tool that enabled me to interact with logs in a fun an engaging way. I could easily ask questions like:

  * What was happening between 11:40 and 11:45
  * Which user makes the most requests? 
  * What are some of the unusual requests?

So how have I done it?

To maximize fun and learning factor, I've decided to do everything in [Docker](https://www.docker.io). 

1. *Getting docker* - I am using [boot2docker](https://github.com/boot2docker/osx-installer) and my first step was updating it.. 

2. *Choosing container* - I've started with [centos](https://registry.hub.docker.com/_/centos/). Unfortunately I had issues with getting ElasticSearch to work (missing commands) and in the end I've decided to try ubuntu. This proved much easier, so ubuntu was my base image.

3. *Base ElasticSearch image* - preparing it was quite easy. The main thing I've learnt was to use ```--rm``` flag to enable container internet connectivity (this was needed in order to access package repositories). I've installed java, apache, ElasticSearch and Kibana. Once I was done, I've made sure to run ```docker commit 8cc7b46cXXXX elasticsearch```.

4. *Running container with ports exposed* - ```docker run --rm -i -t -p 80:80 -p 9200:9200 elasticsearch /bin/bash```

5. *Uploading CloudTrail logs* - I've found [cloudtrail-elasticsearch-import project](https://github.com/mostlygeek/cloudtrail-elasticsearch-import) on github, which made it quite easy and matter of running ``` node import.sh.js  --elasticsearch http://IP:9200  --bucket BUCKET_NAME -r REGION -p PATH/2014/06/ ```

6. *Profit* - At this stage I had my Kibana dashboard ready for me to start playing with.

## Benefits of Docker

A few days later, I've decided to play with ElasticSearch a little bit more. 

Nice side effect of using Docker - I still had my ElasticSearch image without any data on it.  I've decided to do something different this time - visualise my bank transactions. 

Long story short, it worked - the main challenge was how to upload exported CSV into ElasticSearch.

Apparently, the way to copy file from my laptop into running container involves netcat:

```
$ docker run --rm -i -t -p 80:80 -p 9200:9200 -p 10101:10101 elasticsearch /bin/bash
container: $ nc -l 10101 > transactions.csv
$ cat transactions.csv | nc IP 10101

```

Once the file was there, I used [elasticsearch-river-csv](https://github.com/AgileWorksOrg/elasticsearch-river-csv) plugin to load it. Just make sure ```elasticsearch``` user has access to that file and can create files in the directory. (As always, looking at /var/log/* helped in understanding what was going on.) 

Happy hacking!