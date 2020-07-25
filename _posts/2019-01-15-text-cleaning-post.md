---
title:  "How To Clean a Text"
date:   2019-1-15
layout: post
categories: programming
---
## How to Clean a Text using R and a tiny amount of Python

### What do you mean by "cleaning" a text?

This post explains how to break down digital .txt files into semantic units of interest-- their paragraphs, sentences and words. A good source for these is [Project Gutenberg](https://www.gutenberg.org/). They ought to be encoded in UTF-8; this is especially important in the "lyrical" realm as lots of members of my corpus like to strut their polyglotism. 

### Why use this method instead of a prebuilt library?

A grounding assumption in literary studies is that there is an artfulness and delicacy attached to each sentence of the works' of great authors such as Doctorow, Dickens and Zola. Their works are remembered because they beautifully command of *words* -- recall Flaubert's "mot juste" and the particular words and actions in Proust that elicit striking memories and emotions from readers -- in addition to complex plots and expertly crafted characters. I'm concerned with synactic relationships, frequencies, etc.; features that manifest themselves through the words utilized in a given text. Great authors "have a way with words" that comes to fruition in memorable sentences and paragraphs. So I don't think it would be right to only analyze a novel's words, isolated from their original context. Indeed, its pretty easy to just fire up the **`tm`** package, make a corpus, and run classifer X, Y, Z and optimize at will. This won't get me the results I'm really after, though. This is where my rough and tumble text scrubbing comes into play.

If you're just trying to classify Amazon Reviews, this is probably too in-depth for what you need. But, if you're in the realm of lengthy texts that are tied together by rhetorical flourishes and sparks of creativity, you may want to consider supplanting the paradigmatic "bag-of-words" approach. (I'm definitely going to use that representation during classification, but I think having these various semantic units will help when I engineer features about my texts).

###### (Hidden First Step... Read in your text...)

**```poe <- scan("rawTexts/edgar-allen-poe-narrative-of-arthur-gordon-pym.txt",what="character",sep="\n")```**

### Step 1: Substitutions and Removals

#### Getting rid of Project Gutenberg Headmatter 

Subsetting does the trick.

**```orlando.start <- which(orlando=="He--for there could be no doubt of his sex, though the fashion of the")```**

**```orlando.end<- which(orlando=="Eight.")```**

**```orlando<-orlando[orlando.start:orlando.end]```**

After this, **`orlando`** is a vector containing each line (literally, each line exactly as it appears) of your text file, in my case, Woolf's *Orlando*. This can be surprising the first time you inspect your vector since it obviously does not connect multiline sentences. More on that later.

##### Remove periods in proper acronyms and abbreviations

Here is my regex for getting rid of an "in-sentence" acronym in *To The Lighthouse*. I discovered this was a problem while cleaning *The Great Gatsby*. The ever genteel Gatbsy boasts a vasts host of swarthy, debonair friends whose names all fashionably sport acronyms. And who can forget the eye doctor, "T.J. Eckleburg." We don't want his name being considered three sentences!

**```light <- gsub('(?<=[A-Z])(\\.)(?=[A-Z]|\\.|\\s)', '', perl=TRUE, light)```**

Some guidance: **`gsub()`** is a function to look through a given text. **`light`** is just a big container for every line contained in *To The Lighthouse*. The first argument is the regular expression I'm hunting for. The second argument is what I'm replacing with -- nothing, in this case, since I want to delete those pesky periods. The **`perl=TRUE`** argument enforces more succinct guidlines in your Regex syntax. It makes **`R`** do its job better, basically. **`light`**, again, is just the book I'm dealing with at that moment.

#### Remove chapter headings and numbers

These can be actual numbers or Roman Numerals. But getting rid of them isn't that difficult. Some books also have "Section" or "Volume" markers; I tend to remove those manually through subsetting since they aren't that frequent.

This is how I get rid of the chapter headings in D.H. Lawrence's *The Rainbow*.

**```rainbow <- gsub('CHAPTER.X{0,3}(IX|IV|V?I{0,3}).|[A-Z]{2,}', '', perl=TRUE, rainbow)```**

Readers of *Moby Dick* will sympathize with the difficulties presented to me by the Cetology chapter! 

#### Remove slashes in dialogue

Sometimes the dialogue of a book gets read into R with an extra "\\" coming before each quotation mark. 

So a sentence might look like: "\\" Hello, how are you today?"\\" said Sybil... I'm not sure why. But it is easy to remove. This **`gsub()`** does the trick.

**```rainbow <- gsub('\"', '' , rainbow)```**

One helpful package I use in this phase is [qdap](https://cran.r-project.org/web/packages/qdap/qdap.pdf). I use the **`replace_abbreviation()`** function which expands strings like "Mr" into "Mister." I'm okay with this transformation because it doesn't really affect the mechanics of a text. However, the function isn't perfect, so I do it last to minimize its impact.

**```rainbow <- replace_abbreviation(rainbow)```**

At this point your text is pretty clean. 

Now, peel off blanks spots.

**```dorian.temp <- which(dorian != "")```**

**```dorian <- dorian[dorian.temp]```**

### Step 2: Transform into "unit" of choice

#### Words

Words are by far the easiest to extract from a text. My work in this matter is thus pretty straightforward. 

**```volc.temp <- volc```**

Regularize the spaces.

**```volc.temp <- paste(volc.temp, collapse=" ")```**

Lowercase everything -- pretty standard. 

**```volc.temp <-tolower(volc.temp)```**

This presents the only tricky part. The next line divides up your temp vector on **`\\w`**, aka *word* boundaries. Which makes sense. However, I don't want to split up a word like "don't" into "don" & "t." That would be silly and kind of skew my word counts. There are a few solution. You could expand every contraction. But that would break the flow of a text; don't and do not sound quite different. Or, you can...hunt StackOverflow and find a proper Regex that does not split up contractions. Quite helpful! The one tricky thing you can run into here is the *encoding* of your apstrophes. 

Believe it or not, unicode has two types of quotes -- what I call "curly" (’) and the standard, "iPhone" quote ('). If you tell **`R`** that your novel's contractions have curly quotes when it really uses iPhone quotes, you're going to have a miserable time debugging when you can't figure out why you're being stuck with "isn" & "t" in your data frame of words. 

I speak from experience, because this problem plagued me for about 45 minutes until I held my computer dangerously close to my face and realized what was happening. Live and learn, ha! 

The "well-formed" line:

**``` volc.temp <- unlist(strsplit(volc.temp, "[^\\w’]", perl=TRUE))```**

Lastly, scrub blank characters. 

**```volc.not.blanks <- which(volc.temp != "") ```**

**```volc.words <- volc.temp[volc.not.blanks] ```**

#### Sentences 

I use a package called [tokenizers](https://cran.r-project.org/web/packages/tokenizers/tokenizers.pdf) for this part. 

It works pretty well, but only because of all the prelimary work I carry out in Step 1.

The basic function call works by first collapsing your vector on new lines.

**``` poe.sents.first <- paste0(poe, collapse = "\n")```**

Next, you **`unlist`** the function output. I have found that feeding the function more than 2500 lines causes it to break, so I often chunk up my text vector into a few pieces. I think the most I needed was with *Under the Volcano*, where I had six chunks.

**``` poe.sents.first <- unlist(tokenize_sentences(poe.sents.first))```**

Now, my main issue with this package is the way it handles dialogue. 
A defintion: In English, "tagged dialogue" looks like this. 

From *To The Lighthouse*: "A bit of a hypocrite?" Mr Bankes suggested

In my view, it is improper to separate these into two distinct sentences. Part of this is because you can ask a question without explicitly tagging who the speaker is. So, it is of interest to me when an author decides to do that. Also, words like "said" or "asked" are frequently used in conjunction with dialogue. It is a mess to keep track of all the "he said" sentences if they are not attached to their antecedents. I haven't dug deep into where the prescriptivist school of grammar lies on this issue, so I could be out of my paygrade here. But, that's the assumption I'm running with my project. 

To reiterate, I think they should travel together because it will make my future work easier when I want to inspect what *particular* speaking verb dialogue is *tagged* with. It's better if everything is contained in one sentence. **`tokenizers`** doesn't agree here, though, as it splits sentences like this in two. It does the same with exclamatory remarks:

From *Lolita*: “Lo!” cried Haze

I fix this with a "patching" for-loop. This checks if a sentence ends with a punctuation mark and the following sentence starts with a lowercase letter. If so, we need to "patch" them together with a simple paste statement.

```
	for(i in seq(1:length(orlando.sents))){
  		test <- substr(orlando.sents[i], nchar(orlando.sents[i]), nchar(orlando.sents[i]))
  		test2 <- substr(orlando.sents[i+1], 1, 1)
 		test3 <- substr(orlando.sents[i], 1, 1)
  			if(test %in% c('?', '!') && test2==tolower(test2) && test3!=tolower(test3))
    			orlando.sents[i] <- paste(orlando.sents[i], orlando.sents[i+1])
    		bad_spots<-append(bad_spots, i+1)
    	}
    }
```    	

After this loop, I delete every index in **`orlando`** that's contained in **`bad_spots`**. 

I also do a second version of this loop, where I check if the last letter of the first sentence is contained within quotation marks. 

Between these two loops, the "count" of sentences from **`tokenizer`** method drops by about 400. This is pretty significant. I think it justifies my patching. And my loops are elegant enough I think. They run quite fast. So no concerns if a doorstopper like *Gravity's Rainbow* is in your corpus; it's in mine, too!

#### Paragraphs

This is the most time consuming representation to come up with. This is because of the way **`R`**handles new lines and wrapped texts... it does a bad job... after lots of headaches and no reliable third-party solutions I just ended up using an old idea from a Python homework from first year. 
 ```Python
    		f = open('james-joyce-portrait-of-the-artist.txt', 'r')
			data = f.read()
			list = re.split('\s{4,}', data)
			array_paragraphs = np.array(list)
			df = pd.DataFrame(array_paragraphs) 
```
Nothing crazy here, I split anytime we have a larger than 4 whitespace, pipe it into a dataframe and then write to a csv. From here, I read that csv back into `R`, rerun all the cleaning and substitutions from Step 1, and continue from there. 

One thing to note here is that I work with the paragraphs inside of a `data.frame` when I put them into `R`. I use `dplyr` to carry out everything from Step 1. Lots of `transmute()` at the end of the day.

```
pym.paragraphs <- pym.paragraphs %>% 
transmute(para = gsub('CHAPTER [0-9]', '', perl=TRUE,paragraph))
```

### Step 3: Commit to Database.

This step is a piece of cake. I use one giant **`SQLite`** table as my "data-lake" that contains all of the important units from my corpus. My four columns are: Title (of the book), Type (whether its a paragraph, sentence or word), ID (a unique identifier; a mashup of title, type and a generated number; serves as Primary Key), and Unit (the unit itself, like "dog" or "How are you?").

I'm using a database so I don't have to keep loading various representations from a giant `.Rdata` file... Instead, query what you like from your database and dispense with it when you're done.  

Persisting to the database is simple; **`R`** makes it easy to integrate a **`SQLite`** instance into your project! Bind your data into a **`data.frame`** and append it to your table. 

```
pym.title <- rep("pym", 625)
pym.para.type <- rep("paragraph", 625)
pym.para.counter<-seq(1, 625)
pym.para.id <- paste0("PYM_", "PARAGRAPH_", pym.para.counter)
print(length(pym.para.id))

pym.para.matrix <- cbind(pym.title, pym.para.type, pym.para.id, pym.paragraphs)
pym.para.df <- as.data.frame(pym.para.matrix, stringsAsFactors = FALSE)
colnames(pym.para.df) <- c("Title", "Type", "ID", "Unit")

con <- dbConnect(RSQLite::SQLite(), ":memory:", dbname="textTable.sqlite")

dbWriteTable(con, "textTable", pym.para.df, append=TRUE, row.names=FALSE)
# Test query
dbGetQuery(con, "SELECT Unit FROM textTable WHERE Type='paragraph' AND Title='pym' LIMIT 2")
dbDisconnect(con)
```

That's the rub, folks. Every text has its own unique representation which brings with it particular advtantages and challenges. Tread lightly, and always check your sanity by outputting recent commands into your **`R`** terminal; you never know when you might have accidentally taken a wrong turn, and it's better to find out early than after you've committed to your database. 

Comprehensive implementations for each of these steps can be found in the [repo](https://github.com/timschott/dmp) for this project. I'm making a file for each book. I've found it hard to make functions within one big file for this type of work; I reuse code sometimes, but often have to tweak it a lot, so it can get confusing scrolling vertically through a 1600 line file where most of the work loooks similar. 

I hope this post helps. My site's SEO isn't great but hopefully a fervent undergrad in the future, wading through the messy online regex resources, stumbles upon this guide.

P.S.

This post was inspired by Brad Pasanek's informative post on [how to make a puzzle poem](https://bpasanek.github.io/puzzlepoesis/fabrication/2018/07/20/Recipe.html). Cheers, Brad!

