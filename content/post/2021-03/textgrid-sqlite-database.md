+++
author = "Khia A. Johnson"
title = "TextGrid ⇢ SQLite database in a few steps"
date = "2021-03-23"
categories = [
    "python",
    "sqlite",
    "praat",
    "corpus-phonetics"
	]
+++

So you want to work with annotated speech? I find many (not all!) purpose-built tools for corpus phonetics to be slow, buggy, inflexible, or incomplete, while simultaneously promising to do way more than I *actually* need. Building a SQLite database from `.TextGrid` files ended up being a straightforward solution, and it wasn't hard to do. Here's a quick tutorial.<!--more-->

### before you start...

I'm going to assume that you know some Python. To do this tutorial, you will need:

- A directory of `.TextGrid` files with consistent tier names (you won't need the audio files, but it's fine if they're hanging out in the same directory)
- Python &mdash; at the time of writing, I'm using 3.9.2 (idk if it matters, but it was installed via Homebrew on MacOS Big Sur 11.2.3)
- A few Python libraries:
	- [`textgrid`](https://github.com/kylebgorman/textgrid) is a useful library that deals with Praat's flagship file format (so you don't have to)
	- [`sqlite3`](https://docs.python.org/3/library/sqlite3.html) ships with Python, so you don't need to install it
	- [`pandas`](https://pandas.pydata.org/) is a fantastic library in the [Scientific Python ecosystem](https://www.scipy.org/), and it interfaces very nicely with `sqlite3`
	- [`os`]() also comes with Python, and we'll use just a couple of things from it
- While not strictly necessary, I like to write [notebook-style Python in VSCode](https://code.visualstudio.com/docs/python/jupyter-support-py), and you might have an easier time following along if you do the same&mdash;each of the blocks below is essentially a "cell"

In your Python script you'll need to start by importing these libraries.

```py
import os
import sqlite3 as sql
import pandas as pd 
from textgrid import TextGrid, IntervalTier
```

### read in your textgrids

My corpus consists of a folder containing one `.wav` and one `.TextGrid` file&mdash;with the same base name&mdash;per talker. Each `.TextGrid` has three consistently named tiers. You could revise the code to deal with differently-named tiers, but that's outside the scope here). In order, the tiers are:

1. `utterance`: orthographic transcriptions in 2-10 second (or so) chunks, separated by pauses and breaths
2. `word`: force-aligned words 
3. `phone`: force-aligned phones&mdash;when you get to querying your database, it will be useful to know that the first phone in a word will have the same onset time stamp at the work, and likewise for the last phone and offset stamps 

First, you'll put together a list of the `.TextGrid` files from the folder where they live. I'm doing this here by setting the textgrid directory to be a local subdirectory, and the use *list comprehension* to get what I need out of that folder. 

```py
tg_dir = 'files'
tg_files = [f for f in os.listdir(tg_dir) if f.endswith('TextGrid')]
```

This next block builds three lists of tuples&mdash;one each for utterances, words, and phones. The `for` loop opens each `.TextGrid` in turn, and uses the `textgrid` library to extract each interval's annotation "mark" and timestamps via list comprehension, along with the file name. It then adds these to the lists created at the top using the `extend()` method. 

```py
utterances = []
words = []
phones = []

for f in tg_files:
    tg = TextGrid.fromFile(os.path.join(tg_dir, f))
    utter_tier = tg.getFirst(tierName='utterance')
    utterances.extend(
    	[(f, i.mark, i.minTime, i.maxTime) for i in utter_tier if i.mark]
    	)
    word_tier = tg.getFirst(tierName='word')
    words.extend(
    	[(f, i.mark, i.minTime, i.maxTime) for i in word_tier if i.mark]
    	)
    phone_tier = tg.getFirst(tierName='phone')
    phones.extend(
    	[(f, i.mark, i.minTime, i.maxTime) for i in phone_tier if i.mark]
    	)
```

### convert to pandas

Pandas makes this part easy. The only "fancy" part is that I'm adding column names.

```py
utterances = pd.DataFrame(
	utterances, 
	columns = ['file', 'utterance', 'utter_onset', 'utter_offset']
	)
words = pd.DataFrame(
	words, 
	columns = ['file', 'word', 'word_onset', 'word_offset']
	)
phones = pd.DataFrame(
	phones, 
	columns = ['file', 'phone', 'phone_onset', 'phone_offset']
	)
```

### building your (first?) database

Now that you have three pandas dataframes ready to go, it's time to create the SQLite database. The following line of code does two things. It the `.db` file does not already exist, it first creates a new empty database. Then, it opens a "connection" to the database. Which you need to do before you interact with it. Here's a bad analogy: you keep some stuff in a drawer, and you need to open it to take any combination of the things out. You don't, however, have to take everything out, when you just wanted the cozy socks... like I said, not a great analogy, but maybe it helps? What you need to know is that the connection is an object you will interact with, and it's called `con`.

```py
con = sql.connect('example.db')
```

Now it's time for pandas to shine! In this next block, each of the pandas dataframes is seamlessly added to your database (via `con`) as a separate table. Each of the tables has enough information that they can be matched up later in whatever way you need, but not so much that there's boatloads of duplicate information. This is a major benefit of *relational databases* over gigantic spreadsheets. 

```py
utterances.to_sql('utterances', con)
words.to_sql('words', con)
phones.to_sql('phones', con)
```
And that's it. You might not know how to work with it yet, but that's how to make a SQLite database from textgrids. 

### a few queries to get started

There are a *lot* of resources out there for learning SQL. I'll give a few basic example queries here. It's yet another opportunity for pandas to shine, as it reads the output of your SQL query directly into a pandas dataframe 🙃

Building up SQL queries is a skill that will take time, but you can do some of the key things needed in corpus phonetics. A few examples! The first one grabs three columns for all instances of the word "like" from the `words` table. I'll use LIMIT 5 in each of the examples below to make the output table looking managable.

```py
pd.read_sql(
    sql = """
	SELECT file, 
	       word, 
	       word_onset
	FROM words
	WHERE word = 'like'
	LIMIT 5
    """, 
    con = con
    )
```
>|    | file                      | word   |   word_onset |
|---:|:--------------------------|-------:|-------------:|
|  0 | VF32A_English_I2_20190213 | like   |       94.41  |
|  1 | VF32A_English_I2_20190213 | like   |      236.496 |
|  2 | VF32A_English_I2_20190213 | like   |      308.669 |
|  3 | VF32A_English_I2_20190213 | like   |      319.561 |
|  4 | VF32A_English_I2_20190213 | like   |      330.35  |

Using LAG and LEAD can get you preceding and following context. This adds columns using some time-series-y windowing, and does so for every phone in the `phones` table. If you add in filtering with a WHERE statement, you'll need to be careful how you nest things, as you want the immediately preceding/following items, *not* the preceding/following item from your already subsetted query.

```py
pd.read_sql(
    sql = """
	SELECT file,
	       phone,
	       phone_onset,
	       LAG(phone) OVER(PARTITION BY file
	                       ORDER BY phone_onset) AS preceding,
	       LEAD(phone) OVER(PARTITION BY file
	                        ORDER BY phone_onset) AS following
	FROM phones
	LIMIT 5
    """, 
    con = con
    )
```
>|    | file                       | phone   |   phone_onset | preceding   | following   |
|---:|:----------------------------|--------:|--------------:|------------:|------------:|
|  0 | VF19A_Cantonese_I2_20181114 | sil     |         29.36 |             | s           |
|  1 | VF19A_Cantonese_I2_20181114 | s       |         29.45 | sil         | a1          |
|  2 | VF19A_Cantonese_I2_20181114 | a1      |         29.58 | s           | n           |
|  3 | VF19A_Cantonese_I2_20181114 | n       |         29.66 | a1          | n           |
|  4 | VF19A_Cantonese_I2_20181114 | n       |         29.72 | n           | i4          |


If you wanted to get word initial phones, you might use something like the following. Note that LEFT JOIN is just one of many kinds of join, which you might be conceptually familiar with if you already use pandas (or the tidyverse in R).

```py
pd.read_sql(
    sql = """
	SELECT phones.file,
	       phone,
	       phone_onset,
	       word,
	       word_onset
	FROM phones
	LEFT JOIN words ON (words.file = phones.file
	                    AND phones.phone_onset = words.word_onset)
	WHERE word_onset
	LIMIT 5
    """, 
    con = con
    )
```
>|    | file                     | phone   |   phone_onset | word      |   word_onset |
|---:|:--------------------------|--------:| -------------:| ---------:| ------------:|
|  0 | VF32A_English_I2_20190213 | S       |        17.455 | stop      |       17.455 |
|  1 | VF32A_English_I2_20190213 | W       |        17.865 | whistling |       17.865 |
|  2 | VF32A_English_I2_20190213 | AE1     |        18.375 | and       |       18.375 |
|  3 | VF32A_English_I2_20190213 | W       |        18.545 | watch     |       18.545 |
|  4 | VF32A_English_I2_20190213 | DH      |        18.855 | the       |       18.855 |

It's also possilbe to use LEFT JOIN along with conditions that leverage BETWEEN, which lets you do "range lookups" (i.e., query what word a phone belongs to). Note that it's much more time intensive that equality based conditions, and thus useful to include a LIMIT.

```py
pd.read_sql(
    sql= """
	SELECT phone,
	       (phone_onset+phone_offset)/2 AS mid,
	       word,
	       word_onset,
	       word_offset
	FROM phones
	LEFT JOIN words ON (words.file = phones.file
	                    AND mid BETWEEN word_onset AND word_offset)
	LIMIT 5                 
    """, 
    con = con
    )
```
>|   | phone   |   mid    | word   |   word_onset |   word_offset |
|---:|--------:|---------:|-------:|-------------:|--------------:|
|  0 | sil     |   17.395 |        |      nan     |       nan     |
|  1 | S       |   17.545 | stop   |       17.455 |        17.865 |
|  2 | T       |   17.675 | stop   |       17.455 |        17.865 |
|  3 | AA1     |   17.775 | stop   |       17.455 |        17.865 |
|  4 | P       |   17.85  | stop   |       17.455 |        17.865 |

So there's a quick tutorial, and some queries to get you started. Pro tip for scouring the internet for help: textgrids are a type of *time series* data, and there are many more people that work with other kinds of time series than there are phoneticians. 

In a future post, I will talk about what comes next... that is, working with audio using libraries like [`pydub`](https://pydub.com/) and [`praat-parselmouth`](https://parselmouth.readthedocs.io/en/stable/).
