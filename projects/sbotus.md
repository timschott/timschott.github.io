---
layout: page
title: sBOTus
permalink: /projects/sbotus
date: 2020-08-05
plaindate: Summer 2020
---

## Introduction

During my time at UVa, I worked as a Research Assistant at [IATH](https://iath.virginia.edu). I used R and Python to analyze Supreme Court opinions. Our group's goal was identifying similarites and differences in the tone and composition of opinions regarding "free speech" across the 20th century. To name just a few of our group's intiatives, we tracked intertextuality by creating a database of shared quotations, performed parts of speech analysis (focusing on gerunds, such as "protesting," in particular) and generated a [search interface](http://freespeech.iath.virginia.edu/FOS_searchKey.html) to make sense of the cases. 

### Corpus Description and Text Cleaning

The files we used came from LexisNexis; I maintain local access to them. My corpus for this project consists of roughly 700 Supreme Court cases ranging from 1950 - 2005. The cases are a subset of the court's total output focusing on First Amendment issues. 

Prior to processing these files through a transformer model, I needed to greatly clean the texts. These files contained numerous artifacts specific to SCOTUS opinions like citations, docket-numbers: "boilerplate-legalese," if you will. Various functions in my [scotus.py](https://github.com/timschott/kratos/blob/master/scotus.py#L258) class handle the cleanup for these cases.

The learning pipeline expects the data to be broken apart into sentences. My ultimate goal is to treat each speaker as their own "source," so my task was to break apart these various files into distinct files per speaker. The case data is initially ingested in terms of paragraphs which I proceed to split into sentences. This way, I have all the writing and oral arguments of Justice Scalia in one .txt file, Justice Kennedy's in another, and so on. In total, this time period saw 30 different justices preside over cases: 

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

To bolster this corpus, I incorporate the associated oral argument for each case. This is thanks to the easy to use [Oyez](https://www.oyez.org/) API; see here for [an example response](https://api.oyez.org/cases/1963/449) and [docs](https://github.com/walkerdb/supreme_court_transcripts#oyez-api). This adds depth to each of the justice's "portfolios."

That part of this sequence was fairly straightforward. The argument transcripts are clearly labeled with speaker names, so I simply match them to the justice list grabbing all requisite data. One foible that I did not initially anticipate was shared last names between Justices and other speakers in oral arguments (usually attorneys or other legal staffers). In hindsight I should have figured that there would be a lawyer across this time period that shared a common last name "White" or "Kennedy." It was actually a lawyer with the last name "Ginsburg" that tipped me off to this discrepancy. Humorously, this lawyer served as counsel for none other than Henry Kissinger in Kissinger v. Reporters Committee for Freedom of the Press. Obviously, his understanding of law and order vary a great deal than Justice Ginsburg! I add a check for this situation [here](https://github.com/timschott/kratos/blob/master/scotus.py#L594). Just like the case files, these transcripts are compsed of paragraphs, so I similarly break them into sentences.

### Text Generation with "gpt-2"

Next, I leverage these case files to explore text generation via OpenAi's second generation transformer model, [gpt-2](https://openai.com/blog/better-language-models/). The most recent iteration, gpt-3, made waves on Twitter this summer, pulling off impressive feats such as[generating Javascript](https://twitter.com/sharifshameem/status/1282676454690451457) on the fly. The newest version is mostly locked down in their proprietary "playground" -- as full access would likely cause mass panic -- but their second generation has numerous open source implementation.

Max Woolf's [tutorial](https://minimaxir.com/2020/01/twitter-gpt2-bot/) helped get the ball rolling. I started out cribbing from his [Colab notebook](https://colab.research.google.com/drive/1VLG8e7YSEwypxU-noRNhsv5dW4NfTGce). However, I found his "gpt2-simple" package to be a bit, well, slim, and wanted a bit more control over the generation process. So I dove into [nsheppard's fork](https://github.com/nshepperd/gpt-2) of Open AI's gpt-2 repo and followed along with Gwern's [lovely guide](https://www.gwern.net/GPT-2) to all things gpt-2. Gwern's working with poetry in their post, and this area of focus naturally intrigues me a great deal.

The existing gpt-2 work was fairly out of date due to differences between tensorflow 1 and 2. I was able to retool the fork to compile okay after a few hours of trouble shooting. You can look at my updates [here](https://github.com/nshepperd/gpt-2/compare/finetuning...timschott:finetuning), mainly it was just turning a standalone class into a vanilla dict and deleting a memory optimization class that's not used for the 117M-sized model. Ideally this will be turned into a clean PR and put back into nsheppard's code, but that's for another day.

From here, I put together a [Colab notebook](https://github.com/timschott/kratos/blob/master/gpt2.ipynb) that finetunes a model for an inputted text file. I used a cloud based set up because getting tensorflow-gpu installed a Mac is extremely tedious.

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

Rather than pursue additional data, I'm opting to leverage my existing corpus in a different way.



