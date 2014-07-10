---
layout: post
title: "Conditional commit when Deploying to Heroku with Semaphore CI"
date: 2014-07-10 21:04:49 +1000
comments: true
categories: heroku semaphoreapp
---

Let's say that before deploying to Heroku we want to check for any assets changes, and in case we find some we want to do some work.

If there are changes, we want to precompile assets and push any changes back to repo.

Here is example deploy script that does it:

<!--more--> 

{% codeblock ci_semaphore_deploy.sh %}

#!/bin/bash

git fetch heroku

[ $(git diff master heroku/master app/assets | wc -l) -gt 0 ] || ASSET_CHANGE=1

if [ -z "$ASSET_CHANGE" ];
then
    git reset --hard
    git config --global user.email "hi@example.com"
    git config --global user.name "semaphoreapp "

    RAILS_GROUPS=assets RAILS_ENV=production bundle exec rake assets:precompile
    git add --all public/assets
    git commit -m "Update asset manifest before deploying [ci skip]"
    git push origin master
else
    echo "No asset changes detected..."
fi

git push --force heroku $BRANCH_NAME:master


{% endcodeblock %}

Interesting bits:

  * Line 3 and 5: To see if there are any changes we need to fetch branches from heroku.
  * Line 9:  Semaphore CI is rewritting ```config/database.yml``` which was problematic combined with ``` RAILS_ENV=production```
  * Line 10,11: We need to do this to make ```git push``` work. Also, you will need to [add extra ssh key](https://semaphoreapp.com/docs/adding-more-ssh-keys.html) to be able to push. 


