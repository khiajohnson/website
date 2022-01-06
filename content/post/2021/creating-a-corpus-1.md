+++
author = "Khia A. Johnson"
title = "Creating a speech corpus #1: Before you begin"
date = "2021-07-20"
tags = [
	"corpus-phonetics",
    "corpus-creation-series"
	]
+++

Before you start collecting data, you need to do some due diligence. Because as important as speech data sets are, they are not trivial to create, and you need to balance what you want from the data with the time and resources you can access. I don't mean to suggest that developing speech data sets isn't important (it is), but rather that it needs to happen after careful consideration. This post gets at some of the things you'll want to think about before you start planning your dream corpus. 

 <!--more-->

This post is part of a [series](/post/2021-06/creating-a-corpus-0/) on what it takes to create a speech corpus and is a compilation of the lessons I learned when I did just that. I created the [SpiCE corpus of Speech in Cantonese and English](https://spice-corpus.readthedocs.io/) for my dissertation research, and you can download it for free!

## Scour the internet

Before you do anything else, look to see if someone else has already collected the thing you want. There are a number of different online repositories that can help your search. Some repositories are tailored to language and speech data, while others house all kinds. Use this list as a jumping-off point, but know that there is no single, central place to find data. It doesn't help that [data citation practices](https://www.nature.com/articles/sdata2018259) are, um, not great... but getting better! 

While some are targeted for the domain of speech or language, others are just general data set aggregators. Both kinds are useful. But know that dedicated speech database aggregator are few and far between. Without further ado, here's a list:

- [Speech and Language Resource Bank](https://www.slrb.net/category/data.html)
- [LRE Map](https://lremap.elra.info/)
- [Language Resources and Evaluation Conference Proceedings](https://aclanthology.org/venues/lrec/)
- [TalkBank](https://talkbank.org/)
- [Linguistic Data Consortium](https://www.ldc.upenn.edu)
- [Kaggle Datasets](https://www.kaggle.com/datasets)
- University libraries (good metadata *and* librarians are your friends)
- [Dataverse](https://dataverse.org/) (💫 my corpus is housed on [Scholars Portal Dataverse](https://dataverse.scholarsportal.info/)!)

You can also find resources with more general purpose search engines, provided you have a good idea what you're searching for. You should know, however, that you will miss a lot a excellent resources by starting here. I think Google scholar is best for browsing papers and researchers, but is kind of mediocre for searching actual data sets. 

- [Google scholar](https://scholar.google.com/scholar?hl=en&as_sdt=0%2C48&q=%22speech+corpus%22&btnG=), with the right key words
- [Google scholar profiles](https://scholar.google.com/citations?view_op=search_authors&hl=en&mauthors=label:corpus_linguistics), again, with the right key words

And then there are the big hitters of corpus linguistics (well, in my brain at least). These are the corpora you see getting used referenced on a regular basis. Some examples include: [The Buckeye Corpus](https://buckeyecorpus.osu.edu/), [CORAAL](https://oraal.uoregon.edu/coraal), [The Bangor Bilingual Corpora](http://www.bangortalk.org.uk/), [ALLSSTAR](https://groups.linguistics.northwestern.edu/speech_comm_group/allsstar2/#!/), [ONZE](https://www.canterbury.ac.nz/nzilbb/research/onze/),  [Mozilla Common Voice](https://commonvoice.mozilla.org/en/about), and a whole slew of "National Corpora," such as the [British National Corpus](http://www.natcorp.ox.ac.uk/). You'll notice a heavy English bias in this list, which reflects both what I am aware of (I'm an English speaker based in North America), as well as an overall bias in the field. [It's no secret that NLP and corpus linguistics are anglocentric](https://thegradient.pub/the-benderrule-on-naming-the-languages-we-study-and-why-it-matters/). 

## Ask a researcher

Say you found a researcher who does really interesting work on a language, and from what you read of their paper, they must be working with conversational recordings. But, you can't access their data for any number of reasons. Step 1 is to ask that researcher if they can share it with you. If the data was collected by university-affiliated researchers, then the audio recordings are almost certainly subjected to ethics review board guidelines (and if not, they 100% should be). While variable, many ethics applications allow researchers to share their data with other researchers, but not to post it freely online. This means that you, the researcher, may be able to use their data. But you have to ask! Find the researchers' websites and send them an email. Then wait. Academics are busy and don't operate on normal people's timelines. Maybe follow up in a few weeks.

It's also important to be ready for a "no" in these cases. There are loads of amazing data sets out there, especially in sociolinguistics and language documentation fieldwork contexts, but the social and political structures cannot be ignored. Data sovereignty is a big and important topic, and even if you want to work with a particular collection, it may not be appropriate for you to do so. Fieldworkers spend years developing rapport with communities, and even when their official ethics approval says it's okay, they have a pulse on the communities they work with, and at the end of the day, if a community doesn't want to share, that's their call.

## Ask your librarian

Say you come across a resource that costs money and you don't want to spend money. Ask your librarian. They have budgets and love it when faculty and students communicate what they want money spent on. This happened to me when I was working on my second qualifying paper. Thanks, [Susan Atkey](https://twitter.com/susanatkey)!

## Consider collecting rather than recording

If you can't find an existing resource by searching and asking, then the next escalation is to collect or compile, rather than record. There are lots and lots of examples of this, including data sourced from:

1. Radio (portions of [ONZE](https://www.canterbury.ac.nz/nzilbb/research/onze/) and the [British National Corpus](http://www.natcorp.ox.ac.uk/))
2. Podcasts (such as [SFUSED](https://www.sfu.ca/people/alderete/sfused.html))
3. TV audio/video (like in [this paper using reality TV audio](https://www.journal-labphon.org/articles/10.5334/labphon.96/))
4. YouTube (check out [Lauretta S.P. Cheng](https://sites.google.com/umich.edu/laurettaspcheng/home)'s [LingTube tools](https://github.com/Narquelion/LingTube))
5. TikTok, Instagram, and other social media audio (though to be honest, I want there to be more of this!)

This kind of data may still be challenging and time-consuming to work with, but takes at least one level of work out of the picture. The source you use will dictate audio quality, which may or may not limit what you can do with the data. 

## Record it yourself

If none of those options work for you, then you'll have to record it yourself. Thanks to the coronavirus, it's still challenging to do this in person (safely) in a lot of places. And your linguistics questions definitely aren't as important as the health of your participants. Assuming we'll be able to put microphones on people and share air again, recording a data set is the subject of the rest of this series. Specifically, the kind of dataset where you exercise full control over the recording process.

Before leaving off this post, there's one last question to address. Say you want to record now. You can collect data sets remotely, whether through targeted recruitment or *crowdsourcing*. While both will be subject to some of the same constraints, most of the literature I know about that discusses the topic focuses on crowdsourcing. So...

### To crowdsource or not?

Crowdsourcing is a way to cover a lot of ground (literally) and get a lot of data quickly. The coronavirus pandemic has accelerated crowdsourcing as an accepted tool in data set development. There is a big challenge, however, and that's audio quality. There are [apps you can use](https://cleanfeed.net/) and [some guidelines about hardware](https://doi.org/10.3389/frai.2020.565682), but at the end of the day, your participants will be using different microphones in different noisy environments. So if you're interested in doing phonetics work, you can say goodbye to analyzing particular variables. This will impact fricatives more than vowels, for example. 

If those limitations are okay with you, then, by all means, crowdsource. There are neat examples of crowdsourced data from [Mozilla's Common Voice](https://commonvoice.mozilla.org/en/about) and [DRAWL](https://blogs.ubc.ca/drawl/) at my home base of UBC.

## Final thoughts

Speech data is great. But it's expensive. Do your homework. Feel free to name your favorite corpus in the comments.

