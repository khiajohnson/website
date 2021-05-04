+++
author = "Khia A. Johnson"
title = "Sibilant trajectories with Python + praat-parselmouth"
date = "2021-05-03"
description = "A sample workflow to collect sibilant trajectory measurements in Python"
tags = [
    "linguistics",
    "python"
]
categories = [
	"tutorial"
	]
+++

Once I've identified a sample of speech sounds that I want to analyze, the next step is to do that analysis. There are obviously many ways to go about this process. Here, I'll walk through an example of measuring sibilant trajectories with the fantastic `praat-parselmouth` Python package. It's my current favorite technique for avoiding Praat scripting.

 <!--more-->

This tutorial post will walk through a workflow with `praat-parselmouth` in Python. I'm going to assume that you already know [Praat](https://www.fon.hum.uva.nl/praat/), and have a speech dataset with:
- `.wav` audio files
- `.TextGrid` annotation files with three tiers (1: utterance, 2: word, 3: phone)

Packages I'll use:
- [`praat-parselmouth 0.4.0`](https://parselmouth.readthedocs.io/en/stable/index.html) for the acoustic measurements
- [`textgrid 1.5`](https://github.com/kylebgorman/textgrid) for reading textgrid files
- [`pandas 1.2.0`](https://pandas.pydata.org/pandas-docs/stable/index.html) for data wrangling
- [`os`](https://docs.python.org/3/library/os.html) (included in Python distribution) for managing file paths
- [`matplotlib 3.3.3`](https://matplotlib.org/) and [`seaborn 0.11.1`](https://seaborn.pydata.org/) for data visualization

```py
import os
import matplotlib.pyplot as plt
import pandas as pd
import seaborn as sns
from matplotlib.gridspec import GridSpec
from matplotlib.lines import Line2D
from parselmouth import Sound
from parselmouth.praat import call
from textgrid import IntervalTier, TextGrid
```

Next up, you'll need to specify where to find your data. I'm using a small data set from the [ALLSTAR corpus](https://groups.linguistics.northwestern.edu/speech_comm_group/allsstar2/#!/)—specifically, sentences from *Le Petit Prince* produced by L1 English speakers. 

```py
tg_dir = "data/ALL_ENG_ENG_LPP/"
tg_files = [f for f in os.listdir(tg_dir) if 'TextGrid' in f]
```

Before making any acoustic measurements, you need to know where to look. An easy way to do this is to read `.TextGrid` files. The following code gets the timestamps for all intervals on the phone tier (here's that's the last of three tiers) with an `S` or `SH` label and saves the pertinent information in a `pandas` dataframe.

```py
sibs = []
for f in tg_files:
    path = os.path.join(tg_dir, f)
    tg = TextGrid.fromFile(path)
    for phone in tg[2]:
        if phone.mark in ['S', 'SH']:
            sibs.append((path.split('.')[0]+'.wav', phone.mark, phone.minTime, phone.maxTime))

sibs = pd.DataFrame(sibs).rename(
    columns={0: 'path', 1: 'sib', 2: 'on', 3: 'off'})

sibs.head()
```
| | path | sib | on | off |
| :--- | :--- | ---: | ---: | ---: |
| 0 | data/ALL_ENG_ENG_LPP/ALL_132_M_ENG_ENG_LPP.wav | S  | 0.193 | 0.313 |
| 1 | data/ALL_ENG_ENG_LPP/ALL_132_M_ENG_ENG_LPP.wav | S  | 1.843 | 1.902 |
| 2 | data/ALL_ENG_ENG_LPP/ALL_132_M_ENG_ENG_LPP.wav | S  | 3.227 | 3.267 |
| 3 | data/ALL_ENG_ENG_LPP/ALL_132_M_ENG_ENG_LPP.wav | S  | 3.597 | 3.687 |
| 4 | data/ALL_ENG_ENG_LPP/ALL_132_M_ENG_ENG_LPP.wav | SH | 6.113 | 6.253 |

<br>

Because I often work with large audio files, it's helpful to preload everything into a dictionary. It takes some time up front but saves a lot of time later on. 

```py
sounds = {}
for p in sibs.path.unique():
    sounds[p] = Sound(p)
```

The last step before making measurements is to write the function that gets them for you. The workhorse used in the function below is `call()` from `parselmouth-praat`—it's a general purpose function that calls on the specified Praat function. While the number, type, and order of arguments will depend on which function you want to use, you can always grab the defaults with the help of Praat's "paste history" tool. In general, using `call()` looks like this:

```py
call(python_object, 'Name of Praat function - no dots', 0, 'ordered', 'args')
```

The function here is a `pandas` "apply" function, which takes `row` as its argument, and applies it to each row in the dataframe. As it's written here, the function divides the specified interval into 16 steps, loads the sound file chunk, processes it according to guidelines in [Yu (2016)](http://asa.scitation.org/doi/abs/10.1121/1.4944992), measures spectral moments at each step, and returns a dictionary. 

```py
def get_spectral_moments(row):
    n = 16
    duration = row['off'] = row['on']
    step_size = duration/n
    data = []
    sound = sounds[row['path']]
    for i in range(0, n+1):
        t = row['on'] + i*step_size
        window = call(sound, 'Extract part', t-0.02, t+0.02, 'Hamming', 1, 'yes')
        pre_emphasis = call(window, 'Filter (pre-emphasis)', 80)
        spectrum = call(pre_emphasis, 'To Spectrum', 'yes')
        cog = call(spectrum, 'Get centre of gravity', 2)
        std = call(spectrum, 'Get standard deviation', 2)
        skw = call(spectrum, 'Get skewness', 2)
        kur = call(spectrum, 'Get kurtosis', 2)
        data.append([{'window': int(i+1), 'pct': i/n, 'dur': duration ,
                    'cog': cog, 'std': std, 'skw': skw, 'kur': kur}])
    return data
```

Now that you have the function, it's time to use it. The following code chunk runs `get_spectral_moments()`, expands the dictionary output, so each measure has a column, and saves the output to a `csv` file. It takes a little bit of time to run. If you're working on the function, and don't need to do a full run every time, make a test dataframe along the lines of `test = sibs.sample(n=5)`, and once things look good, run it on everything. 

```py
sibs['pct_moments'] = sibs.apply(get_spectral_moments, axis=1)
sibs = sibs.explode('pct_moments')
sibs = pd.concat([sibs[['path', 'sib', 'on', 'off']],
                    sibs.pct_moments.apply(pd.Series)[0].apply(pd.Series)], axis=1)
sibs.to_csv(os.path.join(tg_dir,'sibs.csv'))

head(sibs)
```
| | path | sib | on | off | window | pct | dur | cog | std | skw | kur |
| :--- | :--- | ---: | ---: | ---: |  ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| 0 | data/ALL_ENG_ENG_LPP/ALL_132_M_ENG_ENG_LPP.wav | S | 0.19 | 0.31 | 1.0 | 0.00 | 0.19 | 5056.18 | 1994.65 | -0.46 |  0.06 |
| 1 | data/ALL_ENG_ENG_LPP/ALL_132_M_ENG_ENG_LPP.wav | S | 0.19 | 0.31 | 2.0 | 0.06 | 0.19 | 6205.59 | 1462.78 | -0.61 |  0.90 |
| 2 | data/ALL_ENG_ENG_LPP/ALL_132_M_ENG_ENG_LPP.wav | S | 0.19 | 0.31 | 3.0 | 0.12 | 0.19 | 6211.34 | 1284.72 |  0.10 |  0.34 |
| 3 | data/ALL_ENG_ENG_LPP/ALL_132_M_ENG_ENG_LPP.wav | S | 0.19 | 0.31 | 4.0 | 0.19 | 0.19 | 6398.85 | 1399.34 |  0.28 | -0.48 |
| 4 | data/ALL_ENG_ENG_LPP/ALL_132_M_ENG_ENG_LPP.wav | S | 0.19 | 0.31 | 5.0 | 0.25 | 0.19 | 6713.47 | 1361.21 |  0.02 | -0.42 |

<br> 

So what do these measurements look like? Well, the following plot depicts the trajectories for each spectral moment. The following code produces the 4-part figure. 

```py
f, axes = plt.subplots(2, 2, sharey=False, sharex=False, figsize=(11, 10))
plt.subplots_adjust(left=0, right=0.9, wspace=0.3, hspace=0.3)

sns.lineplot(data=sibs, y="cog", x="window", hue='sib',
             err_style="band", ci=95, ax=axes[0, 0], legend=False)
axes[0, 0].set_title('(A) Center of gravity')
axes[0, 0].set(ylabel='Hertz', xlabel='Window')

sns.lineplot(data=sibs, y="std", x="window", hue='sib',
             err_style="band", ci=95, ax=axes[0, 1], legend=False)
axes[0, 1].set_title('(B) Standard deviation')
axes[0, 1].set(ylabel='Hertz', xlabel='Window')

sns.lineplot(data=sibs, y="skw", x="window", hue='sib',
             err_style="band", ci=95, ax=axes[1, 0], legend=False)
axes[1, 0].set_title('(C) Skewness')
axes[1, 0].set(ylabel='', xlabel='Window')

sns.lineplot(data=sibs, y="kur", x="window", hue='sib',
             err_style="band", ci=95, ax=axes[1, 1], legend=False)
axes[1, 1].set_title('(D) Kurtosis')
axes[1, 1].set(ylabel='', xlabel='Window')

f.legend([Line2D([0], [0], lw=1, ls='-'), Line2D([0], [0], lw=1, ls='-')],
         labels=['English /s/', 'English /ʃ/'], fontsize='large', bbox_to_anchor=(1.08, 0.5))

```

![A four-panel figure depicting the four spectral moments, each with window on the x-axis. Panel A shows center of gravity with Hertz on the y-axis. Panel B shows standard deviation with Hertz on the y-axis. Panel C shows skewness with a unitless y-axis. Panel D shows kurtosis with a unitless y-axis. All panels show line plots with error bars in two colors. Blue representes [s] and orange represents [sh]. The line are mostly overlapping and jagged, given the small sample size. ](/images/parselmouth-sibilants.png)

Naturally, this plot could use a bit of work, but you get the idea! The trajectories are all over the place—partly because there was zero checking for errors. But also because there are way more [s] tokens. Take this tutorial as code to get you going, rather than a finished product!