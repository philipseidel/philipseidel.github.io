---
layout: post
title: Dreamhost Migration
categories:
- Posts
tags: []
status: publish
type: post
published: true
---
The last couple of years when I received my annual Dreamhost bill I always asked whether or I really need to be wasting my money on this service? While it is nice to have a remote shell available from time to time I really only use it for wordpress and a few random scripts that I have written over the years.  

Back in the day Dreamhost was very useful as I could store source code, pictures, and other files remotely (which got shutdown a few years back once they introduced the 50GB backup user cap). This was a big deal because many of the cloud backup services didn't exist back then so it was comforting to know that I had copies of important data offsite.  These days with all the backup and cloud providers out there I no longer have the need for this anymore.  My source code can be stored at github.com, files are backed up with (name cloud backup provider here), and there really aren't too many things I need a remote shell for anymore.  I found myself trying to make use of the features available at Dreamhost rather than just using the free services that were out there.

So that brings us to today...  I have decided to move [www.philipseidel.com](http://www.philipseidel.com/) to github.com since I really don't need any of the features present in WordPress.  Github supports Jekyll which makes templating pages and local development extremely easy so the migration was a realively painless experience.  The best part though is that everything is in a git repo so it is versioned and stored offsite so publishing is as easy as a `git push`.  Github also allows for a custom domain to point to the repo which is a plus.

<!--more-->

###Migrate Content

1. Install rvm
2. Install jekyll gem
3. Create repo
4. Export blog
5. Download all assets
6. Create templates
7. Update metadata
8. Update resource references
9. Add CNAME file
10. Commit and push repo
11. Wait 10 minutes for changes to be reflective

###Migrate DNS

Once all the content has been migrated you can pick a DNS provider and start the migration process.  I chose hover.com for DNS because they are cheap and I don't need too many features.  While hover.com does have an unofficial API my current router wouldn't work with it out of the box as I would need to schedule some cron tasks which isn't ideal.  Instead of going down the cron task route I decided to just sign up for DuckDNS and create a CNAME record which allows for native dynamic DNS updates with the Tomato firmware.  The overall process took less than 1 day to complete and once the registration was transferred and TTLs had expired I deleted everything from my Dreamhost account and closed it out.  