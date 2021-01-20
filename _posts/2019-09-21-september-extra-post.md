---
title:  "MaRkDown: Displaying R Code on Github Pages posts."
date:   2019-09-21
layout: post
category: programming
description: Integrating Github pages with RStudio.
---

## Or, R To The Rescue, Again!

In my data science class, DS 4001, taught by Prof. Abigail Flower we will be publishing all of our reports and project papers using RMarkdown. So, I wanted to try it out and see if I can get an RMarkdown document to render on my thesis site. It is a wonderful resource I had no idea existed! I was wondering, a lot, about what the best way to show people the actual code I've been using on my project. Markdown's normal inline code display is alright, but RMarkdown is obviously going to be the best way to demonstrate my hacks.

So, enjoy! Here are the first nine lines of **Heart of Darkness** as read into my corpus. Still working out how exactly I'm going to clean up my files. At the very least I have all the necessary .txt's on my machine.

``` r
setwd("~/Documents/7thSemester/dmp/corpus")

heartOfDarkness <- scan("conrad-heart-of-darkness.txt",what="character",sep="\n")
heartOfDarkness.start<- which(heartOfDarkness == "I")
heartOfDarkness.end <- which(heartOfDarkness == "sky--seemed to lead
 into the heart of an immense darkness.")

heartOfDarkness<-heartOfDarkness[heartOfDarkness.start: heartOfDarkness.end]
print(heartOfDarkness[1:10])
```

    ##  [1] "I"                                                                      
    ##  [2] "The Nellie, a cruising yawl, swung to her anchor without a flutter of"  
    ##  [3] "the sails, and was at rest. The flood had made, the wind was nearly"    
    ##  [4] "calm, and being bound down the river, the only thing for it was to come"
    ##  [5] "to and wait for the turn of the tide."                                  
    ##  [6] "The sea-reach of the Thames stretched before us like the beginning of"  
    ##  [7] "an interminable waterway. In the offing the sea and the sky were welded"
    ##  [8] "together without a joint, and in the luminous space the tanned sails"   
    ##  [9] "of the barges drifting up with the tide seemed to stand still in red"   
    ## [10] "clusters of canvas sharply peaked, with gleams of varnished sprits. A"
