+++
author = "Khia A. Johnson"
title = "A handful of categorical variable coding resources"
date = "2021-03-09"
tags = [
    "rstats",
	]
+++

Categorical variables are something I think about a lot in my psycholinguistic research, and they aren't often given enough time in introductions to mixed effects modeling. I had originally planned to do a write-up on them here, but have now found enough good resources&mdash;papers and talks&mdash;that I think I'll just list them with some accompanying comments. <!--more-->

If you want to understand why categorical variables pose a problem... watch [Laurel Brehm](https://twitter.com/drlearnasaurus)'s AmLap 2020 talk, or read through her materials. [It's all on OSF](https://osf.io/jkpxt/). At one point in the talk, she notes that if you interpret things correctly, you should come to the same inference regardless of whether you use *treatment* or *effect* coding. The problem arises from people not knowing how to properly interpret things. 

If you want to get a quick primer on a lot of different choices, the UCLA Institute for Digital Research & Education's Statistical Consulting website has a (research-)life-saving FAQ page. A couple pages of note are:

- [R Library Contrast Coding Systems for categorical variables](https://stats.idre.ucla.edu/r/library/r-library-contrast-coding-systems-for-categorical-variables/)&mdash;but note that dummy=treament and deviation=effect=contrast=sum coding (imo the confusing redundant names are 💯 part of what makes this topic inaccessible)
- [FAQ: What is effect coding?](https://stats.idre.ucla.edu/other/mult-pkg/faq/general/faqwhat-is-effect-coding/)
- [FAQ: What is dummy coding?](https://stats.idre.ucla.edu/other/mult-pkg/faq/general/faqwhat-is-dummy-coding/)

If you're ready for a deeper dive, there's an excellent paper on the topic that walks you through how to build categorical variable coding from scratch, specifically to match your research questions and hypotheses. You can find that here:

>Schad, D. J., Vasishth, S., Hohenstein, S., & Kliegl, R. (2020). How to capitalize on a priori contrasts in linear (mixed) models: A tutorial. Journal of Memory and Language, 110, 104038. <https://doi.org/10.1016/j.jml.2019.104038>

While there's obviously a lot more to say on the topic, I think some useful questions to ask yourself at the beginning are:

1. What comparisons do you really care about?
2. Which comparisons would you be okay not testing?
3. Is the grand mean for your particular parameter meaningful? 
4. Do you have interactions between categorical variables? 

If you are working with interactions, it's probably a good idea to choose a contrast coding system that sums to zero. Schad et al. (2020) has a useful bit about that on page 17. You can check yours with a simple line of code:

```r
colSums(contr.treatment(3))
colSums(contr.sum(3))
```

If you run the above R code, you'll see that treatment (aka dummy) coding doesn't, but sum (aka deviation/effect/contrast) does. While this summing to zero business is a key feature of sum coding, it is not unique to sum coding. For example, see helmert and repeated contrasts:

```r
colSums(contr.helmert(3))
colSums(contr.sdif(3)) # from the MASS package
```

The takeaway from this rambling post, is that you should set your contrasts. R's default is dummy/treatment, and it's often not a great choice for psycholinguistics research, for all the reasons in Laurel Brehm's talk and the Schad et al. (2020) paper. 
