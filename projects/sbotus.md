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

The files we used came from LexisNexis; I maintain local access to them. My corpus for this project consists of roughly 700 Supreme Court cases ranging from 1950 - 2005, all of which focus on First Amendment issues.

Prior to processing these files through a transformer model, I needed to greatly clean the texts. These files contained numerous artifacts specific to SCOTUS opinions like citations, docket-numbers: "boilerplate-legalese," if you will. Various functions in my [scotus.py](https://github.com/timschott/kratos/blob/master/scotus.py#L258) class handle the cleanup for these cases.

The learning pipeline expects the data to be broken apart into sentences. My ultimate goal is to treat each speaker as their own "source," so my task was to break apart these various files into distinct files per speaker. The case data is initially ingested in terms of paragraphs which I proceed to split into sentences. This way, I have all the writing and oral arguments of Justice Scalia in one .txt file, Justice Kennedy's in another, and so on. In total, this time period saw 30 different justices preside over cases: 

*	WHITTAKERsents.txt
*	BLACKMUNsents.txt
*	BLACKsents.txt
*	BRENNANsents.txt
*	BREYERsents.txt
*	BURGERsents.txt
*	BURTONsents.txt
*	CLARKsents.txt
*	DOUGLASsents.txt
*	FORTASsents.txt
*	FRANKFURTERsents.txt
*	GINSBURGsents.txt
*	GOLDBERGsents.txt
*	HARLANsents.txt
*	JACKSONsents.txt
*	KENNEDYsents.txt
*	MARSHALLsents.txt
*	MINTONsents.txt
*	OCONNORsents.txt
*	POWELLsents.txt
*	REEDsents.txt
*	REHNQUISTsents.txt
*	SCALIAsents.txt
*	SOUTERsents.txt
*	STEVENSsents.txt
*	STEWARTsents.txt
*	THOMASsents.txt
*	VINSONsents.txt
*	WARRENsents.txt
*	WHITEsents.txt

Here are the first few sentences from Justice O'Connor.

```
tim@tims-mbp sents (master) $ cat OCONNORsents.txt | head
```
1. this case requires us to decide whether allegedly obscene magazines purchased by undercover officers shortly before the warrantless arrest of a salesclerk must be excluded from evidence at the clerks respondent was convicted of distribution of obscene materials in violation of
2. the court of reversed the conviction and ordered the charges dismissed on the ground that the magazines were improperly admitted in evidence
3. the court of denied certiorari

From here some of the omissions are apparent. For instance, I removed most proper nouns, like "California," quotes longer than three words, along with plenty of other characters... look at this [function](https://github.com/timschott/kratos/blob/master/scotus.py#L101) for the specifics.  

To bolster this corpus, I incorporate the associated oral argument for each case. This is thanks to the easy to use [Oyez](https://www.oyez.org/) API; see here for [an example response](https://api.oyez.org/cases/1963/449) and [docs](https://github.com/walkerdb/supreme_court_transcripts#oyez-api). This adds depth to each of the justice's "portfolios."

That part of this sequence was fairly straightforward. The argument transcripts are clearly labeled with speaker names, so I simply match them to the justice list grabbing all requisite data. One foible that I did not initially anticipate was shared last names between Justices and other speakers in oral arguments (usually attorneys or other legal staffers). In hindsight I should have figured that there would be a lawyer across this time period that shared a common last name "White" or "Kennedy." It was actually a lawyer with the last name "Ginsburg" that tipped me off to this discrepancy. Humorously, this lawyer served as counsel for none other than Henry Kissinger in Kissinger v. Reporters Committee for Freedom of the Press. Obviously, his understanding of law and order vary a great deal than Justice Ginsburg! I add a check for this siuation [here](https://github.com/timschott/kratos/blob/master/scotus.py#L594). Just like the case files, these transcripts are compsed of paragraphs, so I similarly break them into sentences.

### Text Generation with "gpt-2"

Next, I leverage these case files to explore text generation via OpenAi's second generation transformer model, (gpt-2)[https://openai.com/blog/better-language-models/]. The most recent iteration, gpt-3, made waves on Twitter this summer, pulling off impressive feats such as(generating Javascript)[https://twitter.com/sharifshameem/status/1282676454690451457] on the fly. The newest version is mostly locked down in their proprietary "playground" -- as full access would likely cause mass panic -- but their second generation has numerous open source implementation. 

Max Woolf's [tutorial](https://minimaxir.com/2020/01/twitter-gpt2-bot/) helped get the ball rolling. I started out cribbing from his [Colab notebook](https://colab.research.google.com/drive/1VLG8e7YSEwypxU-noRNhsv5dW4NfTGce). However, I found his "gpt2-simple" package to be a bit, well, slim, and wanted a bit more control over the generation process. So I dove into [nsheppard's fork](https://github.com/nshepperd/gpt-2) of Open AI's gpt-2 repo and followed along with Gwern's [lovely guide](https://www.gwern.net/GPT-2) to all things gpt-2. Gwern's working with poetry in their post, and this area of focus naturally intrigues me a great deal.
