---
layout: post
title: "Some guidelines for writing web scrapers"
summary: "easier than scraping a hide, I suppose"
tags: ['programming']
date: 2021-12-02

---

I've been working on [www.rtljobs.com](https://www.rtljobs.com), a job board for RTL and FPGA engineers, since September. Right now, RTLjobs is an aggregator. We index open FPGA/RTL roles at a number of companies, check them for relevance, and post them to our site. I've already written two job scraping systems to help ease the work of indexing jobs. Here's some general rules I've come to hold for writing web scrapers and other ETL systems. 

# Be nice to your data sources. 

This should be obvious to anyone who's ever had to run a server. In case you haven't: _use politeness intervals, for god's sake_. Your job scraping mission is important, but there's a sysadmin on the other end with a corporate card, a CloudFlare account, and a boss who will get on her bacon if there's an outage. Have some empathy for that person. There's no reason you can't wait a second (or even two!) between successive hits on their website. 

If you don't, you'll ultimately be the one who pays for it - likely by being blocked from their job pages by CloudFlare or some such similar software. 

# Whatever you do, don't interrupt the scrape.

When you're reading >1000 results a day, and you're inserting generous politeness intervals, an end-to-end scrape is expensive and time consuming. As such, you don't want to interrupt it. Your exception handling should be **air**. **Tight**. 
    
Dead job pages, changes in search results, or other unexpected variations in the order of your scraped data should not result in the program rolling over and dying. 

The way I’ve dealt with this is some exception handling code that tags weird results for human review: 

```python
try:
	# rules[key] contains function pointer to a string transform function
	data_xfm[key] = rules[key](data[key])
except Exception as e:
	logging.exception(e)
	logging.error(f"Problem with keying/rule [key='{key}']")
	logging.error(data)
	data_xfm[key] = data[key]
	# Flags post for human review
	data_xfm['post_status'] = 5
	continue
```

That way, I can run a sql query for `post_status = 5` to find which posts had problems getting indexed, make some manual updates to fix the posting and the scraper to prevent it from happening again. 

If, for some reason that happens to fail, I also have a nice greppable error in my logs. Should the SQL store fail for some reason, the log should have the link that failed, which I can feed into the pipeline independently to test for failure. 

# Make it easy to run subsets of your pipeline. 

Since an end-to-end scrape is so time consuming, it’s a really good idea to make it easy to run smaller chunks of your scraper. One relatively easy way to do this is to have the option to run on a subset of inputs. 

For example: my scraper looks at a variety of different job pages for input. The first version had to go through _all_ of the job pages for all of the registered companies during every run of the script. Being able to run the scraper on just one of those companies saves a ton of runtime. (It was also a convenient excuse to learn about the `argparse` Python module.) Now that the second scraper version is complete, I can pass `-c <company>`, and have the scraper only run on `<company>`'s job pages. This has been a tremendous asset as we add new sites for the RTLjobs scraper to index. Instead of running the entire scraping pipeline, I can run it on a single new job site, and confirm that the output matches my expectations. 

# Write your software in stages. 

Another useful way to help cut down on some of the iteration time of your indexing pipeline is to stage your scrape. The first RTLjobs.com scraping utility was a massive, mono-file Python script. It worked, but it had some warts:

* It was hard to figure out where there was a problem when things went wrong. I referred to this as "tangled in purpose" to a software engineer friend, who suggested "separation of concerns" as the more computer-y term.
* It was hard to add more functionality when I found a jobsite that stored data in a new way. (Aside: _wow_, are there a lot of weird ways that job sites store data.)  
* It was hard to optimize for faster runtime. 

Fortunately for me, I stumbled across the concept of an [extract-transform-load process](https://en.wikipedia.org/wiki/Extract,_transform,_load) just as I finished writing the first version of the scraper. I learned a lot from reading about the ETL model. It’s not an exact fit for my use case, but it has some principles that apply nicely to web scraping. The main one that landed for me was ETL being a staged process. I could see a few ways that a staged pipeline could make my life easier, so I decided to leverage that! 

The second go round, I wrote the scraping pipeline in three stages: `extract.py`, `transform.py`, and `load.py`. There’s also a precursor stage, `search.py`, which grabs lists of job links for the scraper to process data from. Each of these stages can function as a more or less standalone script; I can provide each one with some input data and see that it's producing the expected output in isolation of all the other stages. I’ve also written it so that each of the subset stages can pipe together, so that it's quick and painless to test that the stages are playing nicely together. 

This also made it easy to integrate all of these scripts together into a single omnibus script. A few `import` statements and an `argparse` class later, and I had a nice wrapper script, `etl_run.py`, to run all of these stages as an end-to-end operation. In theory, you should get the same output from `etl_run.py` as running all of the stages standalone with pipes tying their inputs and outputs together. One of these days, I need to get around to testing how closely their output aligns. (It should be identical - theoretically, at least!)

One final note on stages: having a staged scraping pipeline gives me the option to change out one of the stages if I ever decide this is something I want to do. I know, for example, that `scrapy` exists, and that it might offer me some benefits - I've just never gotten around to trying it out. If I do decide to, I won't have to bend over backwards to do it. I'll just have to figure out how to make `scrapy` emit data in the same format as my current `extract` stage. 

# Grab more than you think you need, then trim. 

One of the main problems I had with the first version of the scraper was trying to tune some CSS selector or XPath statement to be _exactly right_. I wanted to grab only the data I needed; nothing more, nothing less. This was one of the major stumbling blocks I had, that was also creating problems with separation of concerns. I was trying to:

1. extract text,
2. trim it to be _just right_ into the format I wanted, and 
3. create rules to do this for 20+ different websites with different data formats. 

This was a patent nightmare to do in a single script. I was adding tons of extraneous `if/else` cases trying to accommodate weird edge cases in a ton of random sites. 

Now that I've migrated the scraper to a more modular design, I've come to realize that it's _way_ easier to be greedy on the frontend. Grab more data than you need in the `extract` stage, then trim and format it in `transform`! If you architect each stage smartly, you can set your script up so that your extract and transform rules are modular to a single file per employer site.

I'm actually rather proud of the way I figured out how to do this - it was a very clever use of function pointers, if I say so myself. I'm saving the details of how I did that for another post, though! Stay tuned!

# Sanitize for sanity.

A principle I've held from the start of this project is get to eliminate as much human review effort from the job sourcing/scraping process as I possibly can. One of the best things I've done to achieve this goal is to sanitize as much data as I possibly can on the input side. Location is a great candidate for this kind of regularization if you're running a job board. It helps your DB queries (and, ultimately, your users) by making sure, if they're searching by city/state/country, that they get all postings by virtue of them being a regular format. I've used a [Python dict to regularize US states](https://gist.github.com/rogerallen/1583593) and [another for countries](https://gist.github.com/carlopires/1261951/d13ca7320a6abcd4b0aa800d351a31b54cefdff4), and they have both worked smashingly! 

I use a process like this to transform two letter country codes into the expnded country name:

```python
def intel_country_transform(string_in):
    temp = string_in.split(',')[-1].split()
    return abbrev_to_country[temp[-1]]
```

The raw location extracted from [this Intel job posting](https://jobs.intel.com/ShowJob/Id/3010389/Pre-Si-Validation-Engineer) is `Primary Location: Santa Clara, CA US`. Passing this string to `intel_country_transform()` yields `United States`, which is much easier to look at in the database and on the website. 

This is another place where it's really useful to write systems that raise flags if they get an input they don't understand. It will help you understand where the irregular chunks of data are popping up, and how to fix them!  

I am still learning a lot about how jobsites store data, and there are plenty of problems yet to solve with indexing job pages. (Figuring out how to extract job posts from Workday sites is principal among these problems. Ugh, Workday!) I'd love to hear from you if there are any other useful methods or techniques you've used for web scraping and indexing.  

