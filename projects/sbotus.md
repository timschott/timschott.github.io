---
layout: page
title: sBOTus
permalink: /projects/sbotus
date: 2020-08-05
plaindate: Summer 2020
---

## Introduction

First, a reminder, from one of the less effective early models I generated during this work...

<img src="/images/powerful_rhetorical_device.png" alt="palette cleanser">
"Speech is a powerful rhetorical device."

During my time at UVa, I worked as a Research Assistant at [IATH](https://iath.virginia.edu). I used R and Python to analyze Supreme Court opinions. Our group's goal was identifying similarites and differences in the tone and composition of opinions regarding "free speech" across the 20th century. To name just a few of our group's intiatives, we tracked intertextuality by creating a database of shared quotations, performed parts of speech analysis (focusing on gerunds, such as "protesting," in particular) and generated a [search interface](http://freespeech.iath.virginia.edu/FOS_searchKey.html) to make sense of the cases. 

### Corpus Description and Text Cleaning

The files we used came from LexisNexis; I maintain local access to them. My corpus for this project consists of roughly 700 Supreme Court cases ranging from 1950 - 2005. The cases are a subset of the court's total output focusing on First Amendment issues. 

Prior to processing these files through a transformer model, I needed to greatly clean the texts. These files contained numerous artifacts specific to SCOTUS opinions like citations, docket-numbers: "boilerplate-legalese," if you will. Various functions in my [scotus.py](https://github.com/timschott/kratos/blob/master/scotus.py#L258) class handle the cleanup for these cases.

The learning pipeline expects the data to be broken apart into sentences. My ultimate goal is to treat each speaker as their own "source," so my task was to break apart these various files into distinct files per speaker. The case data is initially ingested in terms of paragraphs which I proceed to split into sentences. This way, I have all the writing and oral arguments of Justice Scalia in one .txt file, Justice Kennedy's in another, and so on. In total, this time period saw 30 different justices preside over its cases: 

*	WHITTAKER
*	BLACKMUN
*	BLACK
*	BRENNAN
*	BREYER
*	BURGER
*	BURTON
*	CLARK
*	DOUGLAS
*	FORTAS
*	FRANKFURTER
*	GINSBURG
*	GOLDBERG
*	HARLAN
*	JACKSON
*	KENNEDY
*	MARSHALL
*	MINTON
*	OCONNOR
*	POWELL
*	REED
*	REHNQUIST
*	SCALIA
*	SOUTER
*	STEVENS
*	STEWART
*	THOMAS
*	VINSON
*	WARREN
*	WHITE

Here are the first few sentences from Justice O'Connor.

```
tim@tims-mbp sents (master) $ cat OCONNORsents.txt | head
```

1. this case requires us to decide whether allegedly obscene magazines purchased by undercover officers shortly before the warrantless arrest of a salesclerk must be excluded from evidence at the clerks respondent was convicted of distribution of obscene materials in violation of
2. the court of reversed the conviction and ordered the charges dismissed on the ground that the magazines were improperly admitted in evidence
3. the court of denied certiorari

These lines make apparent some of the omissions from cleaning. For instance, I removed most proper nouns, like "California," quotes longer than three words, along with plenty of other characters ... look at this [function](https://github.com/timschott/kratos/blob/master/scotus.py#L101) for the specifics.

To bolster this corpus, I incorporate the associated oral argument for each case. This is thanks to the easy to use [Oyez](https://www.oyez.org/) API. See here for [an example response](https://api.oyez.org/cases/1963/449) and [docs](https://github.com/walkerdb/supreme_court_transcripts#oyez-api). This adds depth to each of the justice's "portfolios."

That leg of the sequence was fairly straightforward. The argument transcripts are clearly labeled with speaker names, so I match them to the justices in my list and grab the requisite data. One foible that I did not initially anticipate was shared last names between Justices and other speakers in oral arguments (usually attorneys or other legal staffers). In hindsight I should have figured that there would be a lawyer across this time period that shared a common last name "White" or "Kennedy." It was actually a lawyer with the last name "Ginsburg" that tipped me off to this discrepancy. Humorously, this lawyer served as counsel for none other than Henry Kissinger in Kissinger v. Reporters Committee for Freedom of the Press. Obviously, his understanding of law and order vary a great deal than Justice Ginsburg! I add a check for this situation [here](https://github.com/timschott/kratos/blob/master/scotus.py#L594). Just like the case files, these transcripts are compsed of paragraphs, so I similarly break them into sentences.

### Text Generation with "gpt-2"

Next, I leverage these case files to explore text generation via OpenAi's second generation transformer model, [gpt-2](https://openai.com/blog/better-language-models/). The most recent iteration, gpt-3, made waves on Twitter this summer, pulling off impressive feats such as [generating Javascript](https://twitter.com/sharifshameem/status/1282676454690451457) on the fly. The newest version is mostly locked down in their proprietary "playground" -- as full access would likely cause mass panic. But the second generation has a few different open source implementations, most notably nsheppard's fork](https://github.com/nshepperd/gpt-2).

Max Woolf's [tutorial](https://minimaxir.com/2020/01/twitter-gpt2-bot/) helped get the ball rolling, but his "gpt-2 simple" implementation was not quite as robust as I would have liked. Instead I relied on the previously mentioned fork from nsheppard. Now, his gpt-2 work was  out of date due to differences between tensorflow 1 and 2. This is sort of remarkable considering his code is around a year and a half old. I was able to retool the fork to compile properly after a few hours of trouble shooting. You can look at my updates [here](https://github.com/nshepperd/gpt-2/compare/finetuning...timschott:finetuning). There are some upgrade instructions for tensorflow floating around, but I opted to just make his code compatible with version 1's API. The main steps I took were deleting a couple imports, flipping a standalone class to a vanilla dict and removing a memory optimization class that is not leveraged for the standard 117Mb baseline model. Ideally this will be turned into a clean PR and put back into nsheppard's code, but that's for another day. And let's hope he doesn't look at my commit messages, which are largely variations of "does this work yet?"...

Gwern's [lovely guide](https://www.gwern.net/GPT-2) to all things gpt-2 also served as a helpful primer while conducting this learning exercise. Gwern, in fact, works with poetry in their writeup, and this area of focus naturally intrigues me a great deal.

From there, I created my own [Colab notebook](https://github.com/timschott/kratos/blob/master/gpt2.ipynb) that finetunes a model for an inputted text file. A Colab notebook is Google's take on the popular Jupyter notebook. I'm a big fan of all things Google cloud and this service is no exception; you're offered free GPU access and the ability to synchronize files from Google Drive to the VM. I used a cloud based set up because getting tensorflow-gpu installed a Mac is extremely tedious.

Feel free to work through the notebook itself, but I'll summarize the important parts of it here. 

You want to start by pairing Google Colab to your Google Drive, in order to easily upload data files to train with. 
```
from google.colab import drive
drive.mount('/content/drive')
```
Next, I clone my fork of nsheppard's gpt-2 code
```
!git clone https://github.com/timschott/gpt-2
```
(Note the syntax: to run a bash command in Google colab use `!`). Recall, you're using a VM in Google Colab! From here `cd` into the repo and pull in the standard "small" gpt-2 model.
```
!python3 download_model.py 117M
```
Now create a container to hold the input for the model.
```
!mkdir ../data
```
This step encodes a new-line separated file of sentences into a format acceptable for the transformer. 
```
!PYTHONPATH=src ./encode.py ../drive/My\ Drive/sents/SCALIA ../data/scalia-lines-v001.txt.npz
```
Now make sure you're on tensorflow 1.x and pull in some extra modules 
```
print(tensorflow.__version__)

!pip install toposort
!pip install hparams
```
You may need to make train.py executable:
```
!chmod +x train.py

```
Now you can train your model: 
Some explanation of parameters:
`learning_rate` is the alpha value -- the lower this is, the more aggressively the model will perform its optimization proces
`save_every` is how often checkpoints of the model will be committed to the `models` directory.
A full readout of different params can be found in [train.py](https://github.com/nshepperd/gpt-2/blob/finetuning/train.py#L27-L53)
```
## get latest code
!git pull

## fine tune the model
!PYTHONPATH=src ./train.py --model_name 117M --dataset ../data/scalia-lines-v001.txt.npz \
--batch_size 2 --save_every 100 --sample_every 100 \
--learning_rate=.005 --run_name scalia_1 --top_p .9
```
Now you're model comes to life! It will output its progression (via average loss) as well as text samples periodically.

As a general rule, any loss lower than 2 signals that you're model is headed towards overfitting. Ideally, you want a gentle progression down the optimization curve. The more data you are working with, the longer but more effective this process will take. My Scalia model finished training in about 2 and a half hours. Recall we're using a GPU here ... this is why local development does not work with this activity!

One tip for this is making sure to have your computer remain "always on." Google Colab will halt your progress if it detects system inactivity. Moreover, on a browser level, you also need to constantly wake the browser plugin or else you'll similarly have your model interrupted. Here is a .js snippet I found on stack overlow that you can drop in your console to automatically click the "connect" button every minute to circumvent this issue. (Note: this is the first *and last* .js snippet I'll ever feature on this site, promise.)

```
function ClickConnect(){
    console.log("Clicked on connect button"); 
    document.querySelector("colab-connect-button").click()
}
setInterval(ClickConnect,60000)
```

After you see suitable model performance, the saved version of the model will be stored in a `checkpoint` directory in the repo. It's a good idea to copy this over to your Google drive for later use. 

```
%cp -avr checkpoint/ ../drive/My\ Drive/sbotus_output
```

I took a night off after this -- we're running through pretty complicated computing processes and it's important not to fry yourself! It's very easy to pull a saved model into a clean Google colab session by just reversing that copy. 

From here we can generate text samples. It's a pretty similar command to creating the model; you'll recognize some of the hyperparameters. The full list is in the [file](https://github.com/timschott/gpt-2/blob/finetuning/src/generate_unconditional_samples.py).

```
!python src/generate_unconditional_samples.py --top_k 36 --temperature 0.75 --nsamples 5 \
--seed 2 --model_name scalia_1 --top_p 0.9
```

Let's look at some of the samples my model generated. *Spoilers: even for a first attempt, they are quite shabby...*

>the other one of the cases that give a straight out answer to that you ten pieces of research

>the first amendment seems to me, that what he said is what you said

>that what you said

>you said the by word "know, which says you cannot stop it up to the word used for it

>and the same word "knowingly" says "germane" is misleading to say, the same statement is

>misleading to say, the word "knowingly" says "germane" you are urging upon us to say that the 

>person is not having a grand jury, that that the grand jury, whether it would not be 

>reasonable to have the grand jury, that the grand jury below, but let you pay the jury below 

>some other jury, that is very clear that the grand jury was going to say that the grand jury be

### Initial Impressions

I set out with the goal of constructing a model for each justice. I wanted to create a "virtual" courtroom where each justice model could play off the other's output and start a cycle of arguments based on generated samples. Unfortunately, a few limitations of my dataset and the model made themselves apparent after I created my first model, `scalia_1`.

Now, any observer of the court knows that Scalia is hardly restrained when it comes to broadcasting his thoughts. [The king of quips](https://www.nationalreview.com/2016/02/antonin-scalia-originality-wit/), or ["a twitter egg,"](https://gawker.com/scalia-is-a-twitter-egg-1714198342) depending on where you fall along the ideological spectrum. His dataset contained approximately 15k sentences, one of the most robust across all the justices. I hoped a set of this size would fraternize nicely with gpt-2's behind-the-scenes machinations. 

However, looking more closely into the data, a great deal of Scalia's sentences stem from oral arguments. Indeed, roughly a third of his sentences originate in written opinions with the remaining two-thirds attributed to oral arguments. The divergent context of these two situations matters a great deal during an unsupervised learning exercise such as this. gpt-2, as well as its next-gen follow-up, are bonafide memorization machines, churning through Wikipedia article, Reddit posts, etc. with aplomb. And the relative stability of those respective mediums serves as a boon for the learning task. The stylistic and rhetorical difference between a written Supreme Court opinion and its corresponding oral argument, in my view, pose a problem for the learning task. By way of example, let's inspect two of Scalia's contributions to the record. 

Here he is in *Federal Election Commission v. Akins*, [524 U.S. 11 (1998)](https://api.oyez.org/cases/1997/96-1590), a case litigating whether an individual could sue for a violation of federal law in regards to a statute enacted by Congress:

>524.US.11_50 19980601 And finally, a narrower reading of "party aggrieved'' is supported by 
>the doctrine of constitutional doubt, which counsels us to interpret statutes, if possible, in 
>such fashion as to avoid grave constitutional questions.  See United States ex rel. Attorney 
>General v. Delaware &amp; Hudson Co., 213 U.S. 366, 408, 29 S.Ct. 527, 535-536, 53 L.Ed. 836 
>(1909); Edward J. DeBartolo Corp. v. Florida Gulf Coast Building &amp; Constr. Trades Council, 
>485 U.S. 568, 575, 108 S.Ct. 1392, 1397-1398, 99 L.Ed.2d 645 (1988).  As I proceed to discuss, 
>it is my view that the Court's  entertainment of the present suit violates Article III.  Even 
>if  one disagrees with that judgment, however, it is clear from Richardson that the question 
>is a close one, so that the statute  ought not be interpreted to present it. 

Scalia's opinion incorporates citations, motions towards forthcoming discussions ("As I proceed to discuss") and contains transition words ("And finally,"). We come to expect these elements appearing in an opinion. 

The back-and-forth style of the oral arguments presents a different rhetorical terrain for the justices. Their interolocutions with counsels from the plaintiffs and defendants are a chance for the justices to learn more about specific elements of an argument and gain depth on a case's context. This does not square with the content of their actual opinions, which synthesize their knowledge of a case's background and the rights set forth in the Constitution and the various ruling in the court's history. 

By way of example, let's contrast that opinion block some of Scalia's contributions to the [oral arguments](https://api.oyez.org/case_media/oral_argument_audio/20166) of *FEC v. Atkins*.

>"To vindicate the right to the information."

>"Which everyone has."

>"We want a statement."

>"It's all buried under the Defense Department or other agencies."

The oral arguments are an expository exercise. They don't typically unearth new ground about the rights of Americans and the Constitution's implications and scope. They serve as an opportunity for justices to learn more about the case's subject matter from the parties involved. Also, like any conversation among a large group of people the flow is frequently interrupted, repeated, etc. So we see lots of oral argument sentences containing dashes to represent interruptions:

>"--we have a right to this... a statute is more important than the Constitution?"

>"--No."

If the point of collecting this data is to train an advanced learning model on the nature and implications of the Constitution's free speech provisions, sentences such as these are hardly revelatory. I need to adjust my strategy. 

### Remedies

There are a few different ways to combat my model's unsatisfactory output. The first option would be to simply gather more data overall -- cases and oral arguments -- beyond my 700 case subset. However, that's a non-trivial task. The best way to go about it would be running the DOM from oyez.org through BeautifulSoup, scraping out all the text, determining the justice that's speaking, etc. The [formatting](https://supreme.justia.com/cases/federal/us/418/683/#tab-opinion) of their opinion's does not lend itself well to this type of work. 

Rather than pursue additional data, I'm opting to leverage my existing corpus in a different way. gpt-2 works best when it's provided a massive, coherent dataset. When aggregated, the opinions in my corpus entail roughly 130k sentences. This is a much more formidable dataset for the model to train on than a few thousand here or there. So, I combine the justice's viewpoints into one, megajustice (sorry for willing that term into existence) to get the best results out of the model. Obviously this sort of entity will lack *ideological* coherence, but I'm mostly intrigued at how it handles the rhetoric and logic of the arguments writ large.

To carry this out I just tweaked my python script to not slot the different justices into separate spots in a list - [the commit's here](https://github.com/timschott/kratos/commit/9abbf0c1b0ec5e5040ae368cfb4ba2b25165f743). Next I updated the Colab notebook to pull in the newly generated sentence list from this proces. I then followed a similar training process from there to much better results. After approximately 11 hours my model's loss fell to just below 2, my target value. 

Now it came time to relitigate the sample generation code. Here's the command I issued; I piped everything into [this file](/documents/one_hundred_samples.txt) for easy traversal later. 

```
!python src/generate_unconditional_samples.py --top_k 40 --temperature 0.9 --nsamples 100 \
--seed 2 --model_name all_justices_1 --top_p 0.9 > ../data/one_hundred_samples.txt
```

Here are some of the interesting ones generated by the model. I include the sample banc they reside in if you want to look at them in the context of their group later on.

>sample 44: money does not represent a generic popular constituency

This sample is somewhat challenging to make sense of. Observers of the court ought to be aware that their stances on money as speech are downright depressing. This sample presents a sort of odd syntactic construction. A "constituency" represents a group of people that share a common representative in a democracy; its the "generic popular" part, especially in relation to money that's throwing me. More and more, the court leans towards a view that money can be construted to stand in for speech. For example, limiting the amount of money an individual can donate in a political campaign via political action committees is an unconstitutional limitation of their first amendment right. I suppose the model is tripping over itself here, rightly picking up on the dominant strain of "money" within the decisions but failing to parse out that, fundamentally, it *is* in fact a different type of phenomenon than speech. 

>sample 27: this free speech is the gravest sense of our free society

This sample stands out because of the use of "gravest." I think it nicely implicates how important free speech is to a free and functioning society as indicated in the close of the sampe.

>sample 89: the purpose of freedom of speech and press is to carry a message to its 
>patrons

This sample strikes me as both wonderfully modern and antiquated. The idea of receivers of a message as "patrons" harkens back to the early 20th century corruption present in rings like New York's Tammany Hall. At the same time, it brings to mind the meteoric rise of the "hustle" economy which is best exemplified by Patreon.com. Patreon lets artists, podcasters, writers, and so on produce content for subscribers known as "patrons." That free speech's purpose is to "carry a message" to patrons squares nicely with the tenets and function of the service. 

>sample 88: a restriction on the collection of money to a speaker or organization is 
>not a permissible time, place, and manner restriction

A "time, place and manner restriction" is a content-neutral limitation on expressive activity. This sentence is an interesting, novel exercise of the test to a speaker's collection of money. I would be fascinated to see the model run with this topic matter. Perhaps its in reference to a political rally that sees a group raising funds for a particular cause; alternatively I could imagine it referencing a religious group passing around a donation plate during a sermon. 

>sample 53: i do not agree with that the constitution protects authors, literature, or 
>information, for they do not think the protection of the first amendment from 
>arbitrary and unreasonable searches and seizures

I picked this sample after looking through the matches for "literature." There weren't that many. But this one is a doozy! We have a grammatical error ("with that the"), a slightly nonsensical "they" (?) furnishing this peculiar claim. The opening salvo "i do not agree [sic] that the constitution protects authors" is particularly interesting given the court's long record of litigations involving literature like the landmark obscenity case Roth v United States in which the court defined obscenity as that which contains a "dominant theme taken as a whole appeals to the prurient interest."

>sample 62: nor can it fairly be said that the court is fully aware of the long 
>history of the first amendment

Sometimes this model is *too* on the nose...I think this should replace "Equal Justice Under Law" on the front of the Supreme Court buidling. 

>sample 70: big appellee uses its billing billing billing billing billing billing 
>billing billing billing billing billing billing billing billing billing envelope to 
>public uses

Hard to tell whether this was generated by feeding in supreme court opinions or internal memos from Deloitte strategy consultants. 

>sample 55: comfavored viewpoints, to include in our pluralistic society a term the 
>first amendment guarantees might require that all be discriminatorily exercised in a 
>marketplace of ideas

The model trips over itself here, constructing an argument for the first amendment speech by drawing us towards its popular (among the court) zone of application known as the "marketplace of ideas." However, in addition to a nonsense word ("comfavored") it references a nonexistent subject ("that all be"). However, given the haphazard construction and application of the marketplace of ideas over time, perhaps gpt-2 isn't doing all that bad.

>sample 99: the critical question then is whether the governmental interest in 
>regulating expression justifies even the incidental restriction on protected expression

I think this is one of the best summations of the issue the court faces when approaching questions of speech. It's quite impressive the model managed to generate this line. 

>sample 27: we therefore have the power to force the government to probe into 
>political beliefs, and in order to monitor and criticize the conduct of the people

I leave you with a sentiment familiar to those who grew up in the COINTELPRO days ...

My next effort is to use the conditional generation script to create a call-and-response output with my all-justics model. 