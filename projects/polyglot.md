---
layout: page
title: Polyglot or Not
permalink: /projects/polyglot
date: 2023-05-20
plaindate: Spring 2023
description: Berkeley MIMS Capstone
---

* [Paper]("https://arxiv.org/abs/2305.13675">)
* [Data](https://huggingface.co/datasets/Polyglot-or-Not/Fact-Completion)
* [Code](https://github.com/daniel-furman/polyglot-or-not)

Can foundation language models be used as multilingual knowledge bases? We evaluate the capacity for foundation models to retrieve encyclopedic knowledge across a wide range of topics in a multilingual setting. 

To support this effort, we 1) produce a new, multilingual dataset containing 303k factual associations in 20 different languages 2) formulate a counterfactual knowledge assessment, “Polyglot or Not” 3) benchmark 5 foundation models in a multilingual setting and a diverse set of 18 models in an English-only setting. 

Massive accuracy differences manifest among open-source foundation models of interest, with Meta’s LLaMA topping both the multilingual and English-only assessments. A quantitative and qualitative error analysis of this top model reveals a significant dropoff in the model’s ability to retrieve facts in languages written in the Cyrillic script, as well as the effect of the density of language-specific Wikipedia articles on factual comprehension. 

Ultimately, we argue that the promise of utilizing foundation language models as bonafide polyglots is greatly diminished when they are tasked with retrieving information in languages other than English. Our research artifacts (code, dataset, and analysis) are entirely open-source, empowering future researchers to perform their own experiments on future foundation language models.