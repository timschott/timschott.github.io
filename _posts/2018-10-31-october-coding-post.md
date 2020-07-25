---
title:  Building a Database with Python and SQLite
date:   2018-10-31
layout: post
categories: programming
---

## A Database Is Born

This month I finalzed strategies to clean my corpus. I split my corpus into three buckets:
1. paragraphs
2. sentences
3. words

I am still using the `tokenizers` package to achieve #2. To achieve #3 I am following alongside Jockers. One tweak that I make is that I use `tokenizers` to add contractions back into the representation, as I believe the difference between I and I'm for example is a meaningful distinction. The first representation, the paragraph, was tricky to formulate in `R`. Just my luck. The `tokenizers` package fell short for me here. So, I jockeyed to `python` since a simple solution exists in that language. 

Here is the python code I am using to break down my corpus into paragraphs.

```python
import numpy as np
import regex as re
import os.path
import pandas as pd

f = open('conrad-heart-of-darkness.txt', 'r')

data = f.read()

list = re.split('\s{4,}', data)

array_paragraphs = np.array(list)

df = pd.DataFrame(array_paragraphs)

df.to_csv("HOD_paras.csv")

```

A lovely split is all it takes to clean up *Heart of Darkness*. It works by looking for greater than 4 white spaces. I then throw the list into a numpy array and finally a pandas dataframe since I know how to then put that into a csv with `.to_csv()`.

The next exciting maneuver I carried out this month was putting my output into a database. `R` has a nice plugin with `SQLite` that makes it easy to add various types of output like vectors and matrices into a durable database. I think that this is important for this project because it makes it very easy to keep track of exactly what my "training"/baseline data is. For instance, without a database, I would probably have a massive RData file on hand that would load up something like one dataframe per text of all relevant data. Concatenating them together into one "mega_df" only to have it dissapear when I'm done with R is frustrating. So, instead, with a database, my "lake" of data never goes away. It persists. This approach was relayed to my by DSI Professor Raf Alvarado. He recently wrote a fun paper about the allure of databases: "Big Data, Thick Mediation, and Representational Opacity." It is worth the read when considering how we got to our current, capitalized saturation of Big Data. 

My database is a single table. Its columns are:("Title", "Type", "ID", "Unit"). "Title" is the title of the work. "Type" lets us know whether the unit is a paragraph, sentence or word. "ID" is the unique identifier for the unit. It follows the form *title_type_UniqueInteger*. "Unit" is the actual piece of information: a word like "darkness" or a sentence like "Hello world."

From here I will watch my database grow and grow. It will contain my entire corpus. Then, whenever I need to pull out certain texts, a query as simple as ```"SELECT * FROM textTable WHERE Title='MobyDick' Type= 'word' AND LIMIT 10")``` can be carried out.