---
layout: page
title: Reading DT Leaves - A Digital Analysis of "Lyrical" Novels.
permalink: /projects/dtleaves
date: 2019-04-14
plaindate: Summer 2018 - April 2019
---

My honors thesis for the University of Virginia Distinguished Majors program consisted of a digital study of "lyrical" novels in order to better define this heretofore amorphous, nebulous term. (Editor's Note: You can read about the month to month work of my thesis over on the [blog](/blog). and view the completed project [right here](/blog/2019-04-12-thesis-is-done.md))

I sourced a group of novels based on publications, recommendations from professors, book jackets. critical surveys, etc. I used detective fiction as a control corpus. This aspect of my project was inspired by Moretti’s corpus experiments in *Canon/Archive* where he contends that genre fiction is rhetorically stable and thus presents a beneficial contrast to the indefinite posture assumed by lyrical works. Ultimately, I worked with a set of 50 books. 

To work with these texts, I split them into their words, sentences and paragraphs with a course of R and Python routines. (This work is all in the [project repo](https://github.com/timschott/dmp/tree/master/scriptsAndDatabase/lyrical_clean)). Then I committed these values to a SQLite database. SQLite was incredibly easy to use; my database schema ended up being nothing more than a single table (following the lead of Raf. Alvarado's concept of the ["data-lake"](http://transducer.ontoligent.com/?page_id=326)).

From there I created a feature set of 31 potential markers of “lyrical” novels. The data points ranged from the simple (number of commas per sentence) to the novel (I calculated a moving-average Type-Token Ratio for each novel in order to combat the skewing effect of input size on this long-standing measure of lexical richness). Here is a snapshot of these features for some of my lyrical fiction.

<img src="/images/data_points_4.png" alt="31 features across a subset of lyrical works">

My idea was to work with my large feature set and use a combination of supervised learning and close-reading to determine the most consequential. I utilized SVM's along with Decision-Trees and followed up these computational techniques with close-to-the-ground literary study. At one point in my project I stepped away from the computer entirely and spent the week poring over *Moby Dick* -- and who wouldn't? I don't need an R subroutine to tell me that sequences such as this are, without a doubt, *lyrical*:

>“Is it not curious, that so vast a being as the whale should see the world through 
> so small an eye, and hear the thunder through an ear which is smaller than a 
> hare's? But if his eyes were broad as the lens of Herschel's great telescope; and
> his ears capacious as the porches of cathedrals; would that make him any longer of
> sight, or sharper of hearing? Not at all.—Why then do you try to "enlarge" your 
> mind? Subtilize it”


Of course I did spend quite a lot of time at my computer during the project; I generated some very interesting visualizations during the project like this graph of syllabic variety across an internal monologue from Quentin Compson in Faulkner's *As I Lay Dying*.

<img src="/images/quent.jpg" alt="Quentin Compson's syllabic profile">

Ultimately, I conclude that the most consequential marker of a lyrical novel is the presence and continued invocation of anaphora.

My thesis modulates between digital sleuthing and the more traditional type of literary study one would expect when approaching an English thesis. I relished leveraging these different modes of investigation towards a common goal. I think the combination generated my project's efficacy. 