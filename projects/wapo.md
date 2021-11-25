---
layout: page
title: @WashingtonBezos
permalink: /projects/wapo
date: 2020-04-03
plaindate: Spring 2020
description: I built twitter bot that tracks mentions of Jeff Bezos in Washington Post articles.
---
My bot tweets whenever *The Washington Post* uses a sentence such as "(Jeff Bezos owns the Washington Post)" in a news story. 

Here is one of its tweets:
<blockquote class="twitter-tweet"><p lang="en" dir="ltr">(Amazon CEO Jeff Bezos owns The Washington Post.) <a href="https://t.co/lobPfkTQm9">https://t.co/lobPfkTQm9</a></p>&mdash; Washington Bezos (@WashingtonBezos) <a href="https://twitter.com/WashingtonBezos/status/1281724955785388044?ref_src=twsrc%5Etfw">July 10, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

To ingest news data, I use the [newsapi](https://newsapi.org/). My process for ingesting article's is a bit roundabout; first I query the news api for any article's from *The Post* containing Bezos's name (they offer a powerful search library). However, they do not provide full article text in their api response; just a truncated version. But they return the URL for the article, which I subsequently run through a manual, [Beautiful Soup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/) scraping routine that traverses the DOM and looks for the invocation and context of Bezos's name. 

My process is constantly improving. At first, the "bot" wasn't automated at all - I would run my script every night *around* 10pm and hope that tweets would appear. In May, though, I fully automated my script to run hourly with a simple setup via [cron-job.org](https://cron-job.org). 

The function itself is deployed via a [Google Cloud function](https://cloud.google.com/functions). I opted to use Google over AWS because I'm kind of sick of the AWS console's aggressively confusing interface and their quite stingy billing practices. Google offered me a year of free function calls and I have not had any trouble reading their docs.

Regarding Twitter, I had never worked with Twitter's API prior to creating this bot. I use [tweepy](https://www.tweepy.org/), a simple wrapper for their API, to actually send the tweets.

Overall I'm quite pleased with how everything fell into place for this project. I've even gone above and beyond as far as monitoring goes -- if there is ever a time that "Bezos" appears in an article but my hornets' nest of regular expressions aren't able to capture exactly where, I get an email alert [right from my python code](https://github.com/timschott/kratos/blob/master/wapo.py#L459-L478).

Going forward, I would like to do a restrospective of sorts and analyze the different types of articles and situations the paper discloses Bezos' owernship; his company's influence astounds me. 