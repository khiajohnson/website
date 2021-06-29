+++
author = "Khia A. Johnson"
title = "You should use tidylog in your #rstats corpus phonetics workflow"
date = "2021-03-29"
categories = [
    "rstats",
    "tidyverse",
    "linguistics",
    "vot",
    "tutorial",
	]
+++

Last week, [I asked #rstats twitter](https://twitter.com/khia_johnson/status/1375205038721658881) for a bit of help with something that has *always* felt clunky in my R code but was never annoying enough to actually fix. In corpus phonetics, you typically start with a large data set, make measurements, and then use informed criteria to filter out errors to the best of your ability, because measurements can be wrong. When you go to share your findings, you need to report how many items were removed (and why). To do this, you have to keep track. Sure, alternating between `filter()` and `print(nrows(df))` works, but it's clunky. I'm starting to think that maybe I should have been annoyed earlier. <!--more-->

Fortunately for me, [#rstats twitter answered very quickly](https://twitter.com/khia_johnson/status/1375208758352089089?s=20) with mostly the same suggestion: use [`tidylog`](https://github.com/elbersb/tidylog). After a first go with the package, I agree. It's easy to use, and the documentation is good. The value-add of this blog post, then, is probably not very high... 🤷🏻‍♀️

Nonetheless, I think that domain-specific examples are helpful. In that spirit, here's an example of how you might fold `tidylog` into your now-more-elegant corpus phonetics workflow! 🚀

## install + load

The `tidylog` package is on CRAN, so it's easy to install. Then, load it along with the tidyverse. The documentation suggests loading `tidylog` *after* `tidyverse`/`dplyr`, as it modifies a bunch of existing functions. Setting `warn.conflicts = FALSE` isn't necessary, but you know there are going to be conflicts, and  that means you don't have to look at the \*very dire\* warnings. 

```r
install.packages("tidylog")
library("tidyverse")
library("tidylog", warn.conflicts = FALSE))
```

## understand your data

Next up, load your data, and think about it, and check out the structure of the data using the handy `str()` function. I have a bunch of text-y variables, as well as two integer variables (where the unit is milliseconds). 

```r
vot <- read_csv("path/to/vot.csv")

str(vot)

# tibble [15,630 × 9] (S3: tbl_df/tbl/data.frame)
# $ talker              : chr [1:15630] "VF19A" "VF19A" "VF19A" "VF19A" ...
# $ language            : chr [1:15630] "Cantonese" "Cantonese" "Cantonese" "Cantonese" ...
# $ word                : chr [1:15630] "天天" "潘" "頭髮" "個" ...
# $ phone               : chr [1:15630] "t" "p" "t" "k" ...
# $ vot                 : int [1:15630] 20 230 51 34 26 ...
# $ following_vowel     : chr [1:15630] "i1" "u1" "a4" "eo5" ...
# $ following_vowel_dur : int [1:15630] 90 120 110 30 50 ...
# $ prev_phone          : chr [1:15630] "sp" "sp" "e3" "sp" ...
# $ prev_word           : chr [1:15630] "萬事如意" "進步" "嘅" "um" ...
```

It's [voice onset time](https://dictionary.apa.org/voice-onset-time) (VOT) data&mdash;a durational measure based on particular landmarks in the speech signal&mdash;for P, T, and K sounds in Cantonese-English bilingual speech. This data comes from the [SpiCE corpus](https://spice-corpus.readthedocs.io/en/latest/), which is an open-access corpus of conversational bilingual speech. It's coming out soon, I promise. The corpus includes audio recordings of interviews, which have been transcribed at the word level (🙏🏼 amazing RAs 🙏🏼), and then force aligned with the [Montreal Forced Aligner](https://montreal-forced-aligner.readthedocs.io/). From there (and this doesn't ship with the SpiCE corpus) I used [AutoVOT](https://github.com/mlml/autovot/)[^1] to improve the accuracy of the VOT measurements. 

So what does this mean? Well, these are excellent tools, but there are errors. Many such errors can be caught with a few sensible filters. For example, AutoVOT specifies a minimum possible value. If the algorithm thinks it equal to exactly the minimum, that's probably an error. On the flip side, if the VOT is extremely high compared to a talker's average VOT: maybe real, maybe laughter. But actually... the participants laughed a decent amount. Takeaway: linguistics is fun(ny). 

This makes for a good start, but knowing a bit more about forced alignment lets me do more. This is multilingual data, and alignment tends to be poor (= more errors) in sentences that mix languages. So if the preceding word is *unknown*, it's more likely than not a word in the other language. 

## keep track of your exclusions

A few random things to take note of:

- Specifying `tidylog::` in front of the function call makes sure you're getting the version with tidy-logging, regardless of the order you load packages in. If you don't want to log things, use `dplyr::` instead (side note: I recently learned that `dplyr` and `MASS` have a super annoying conflict that has thrown a wrench in many an R analysis...and `dplyr::` is a solution to it).
- If you include multiple conditions within the same call to `filter()`, then it all gets lumped together. If you want counts by each condition, then keep things in separate `tidylog::filter()` calls.
- Logging doesn't print out specific values anything&mdash;so say you wanted to compute the grand mean (rather than by talker, as I do here), you could easily filter by it but would need to save a very-repetitive-column to be able to see what that value was. There are alternatives, and probably better ways to do this, but the data sets I work with are rarely big enough for this to matter. 

So without further ado, this is what it looks like, and what it prints out! Elegant as.. pie 🫐🥧

```r
vot_filtered <- vot %>%
  tidylog::filter(prev_word != '<unk>') %>%
  tidylog::filter(vot > 15) %>%
  tidylog::filter(following_vowel_dur > 30) %>% 
  tidylog::group_by(talker) %>%
  tidylog::mutate(upper = (mean(vot) + 2.5*sd(vot))) %>%
  tidylog::filter(vot <= upper) %>%
  tidylog::ungroup()

# filter: removed 974 rows (6%), 14,656 rows remaining
# filter: removed 702 rows (5%), 13,954 rows remaining
# filter: removed 1,525 rows (11%), 12,429 rows remaining
# group_by: one grouping variable (talker)
# mutate (grouped): new variable 'upper' (double) with 34 unique values and 0% NA
# filter (grouped): removed 229 rows (2%), 12,200 rows remaining
# ungroup: no grouping variables
```

And that's honestly it. There's more to this analysis, but you will have to wait until I share a preprint of the paper! The takeaway for now is that linguists in general, and corpus linguists in particular, should build `tidylog` into their workflows.


[^1]: If you're getting started with AutoVOT, I'd recommend checking out [Eleanor Chodroff's tutorial](https://eleanorchodroff.com/tutorial/autovot.html). As a part of this project, I also wrote a number of Python helpers do do some of what Eleanor's tutorial covers with Praat scripts&mdash;I'll blog about that eventually. If that's something you'd find useful, lmk!
