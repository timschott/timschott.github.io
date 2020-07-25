---
layout: page
title: logomancing
permalink: /projects/logomancing
date: 2020-01-15
---

<img src="/images/logomancing.png" alt="website screenshot" display:block>

[logomancing.com](logomancing.com) displays particularly peculiar words I come across while reading fiction and non-fiction. Typically, if I find a fun word, I'll jot it on the inside cover of the book. This site documents such terms. Eventually, I hope to leverage my list in a digital project, perhaps exploring the rhetorical and narrative situations that writers employ choice words. 

I developed *logomancing* with Node.JS for the server. It stores its data in a MongoDB NoSQL database. The front-end code is templated in Handlebars, and I used Boostrap to decorate and design elements. The interface is fairly clean, and was the first time I had really applied CSS and .js en masse -- I've still go a ways to go but the project was great practice. The website is deployed on Heroku with a (free!) TLS certificate from Cloudflare. My work is published on npm. Heroku poses a quick and simple alternative to a fully-loaded EC2 deploy, and I love the built in git hooks!

The full repository for this project can be found on my Github. You'll note that the repo is named logomancer ... that's because I broke my original domain name beyond repair trying to wade through AWS Route53. I like the gerund, though. It's an active world! Definitions are from the OED whenever possible. 