---
layout: post
title: "Setting up PostGIS on Semaphore CI"
date: 2014-07-09 23:50:27 +1000
comments: true
categories: postgis ruby
---

Example how setup semaphoreapp.com CI so that it can run tests requiring postgis:

<!--more--> 

{% codeblock ci_semaphore.sh %}

#! /bin/bash
set -e

sudo apt-add-repository -y ppa:ubuntugis/ppa
sudo apt-get update
sudo apt-get install -y postgresql-9.3-postgis
createdb $DATABASE_NAME_TEST -U $DATABASE_POSTGRESQL_USERNAME

psql -c 'CREATE EXTENSION postgis;' -U $DATABASE_POSTGRESQL_USERNAME -d $DATABASE_NAME_TEST
psql -c 'CREATE EXTENSION postgis_topology;' -U $DATABASE_POSTGRESQL_USERNAME -d $DATABASE_NAME_TEST
bundle install --deployment --path vendor/bundle

bundle exec rake db:setup
bundle exec rake db:test:prepare

{% endcodeblock %}

I coundn't google it myself, so I hope this helps!