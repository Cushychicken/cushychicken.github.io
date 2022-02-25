---
layout: post
title: "More notes on writing web scrapers"
summary: "it's amazing what you can learn if you do something daily for months"
tags: ["programming"]
date: 2022-02-25
---

I got a lot of great feedback from
[my first post about writing a web scraper](https://cushychicken.github.io/rules-for-web-scrapers/)
for [RTLjobs.com](https://www.rtljobs.com). The months since then have produced
a ton more work, and a ton more learning about web scraping. Here’s round two of
what I’ve learned about scraping and indexing web results.

# If you can avoid scraping: do so!

It’s always possible to scrape raw job data out of a web page. It’s also really
time consuming, when you account for:

- Parsing HTML,
- Waiting for JS-loaded content to populate,
- Politeness intervals.

I’ve since learned that there are many easier and faster ways to get this data.
Plenty of job platforms have come around to building public facing REST APIs for
their data. This lets you get a whole chunk of job data in one fell swoop.

If anyone from Lever, Greenhouse, or Workable are reading this: bless you.

If anyone from Taleo, ICIMS, or Workday are reading this: a curse on your
houses. Getting job data from your platforms is comparably enjoyable to
receiving amateur dentistry. It makes my teeth hurt just thinking about it.

If you’re clever, there _are_ ways to get the data you need, through a digital
back alley of sorts. Jon Nguyen from [NoWhiteboard](https://nowhiteboard.org)
helped me out with this one. The Networks tab in Chrome/Firefox can help you
find the underlying backend API. A little toying around with the params and
`curl` can help you figure out how to call and parse the results, too. I’ve used
this to effectively hack my own API access to a number of job sites.

# Minimize the amount of configs you keep in code.

Aside from its slowness at runtime, part of what makes HTML based scraping so
time consuming is the amount of human interaction needed to pull it off. One has
to:

- Navigate to a webpage,
- figure all the CSS selectors you need for your required data,
- enumerate those selectors in code,
- Test them for correctness,
- Tweak as necessary….

Every single attempt I’ve made to turn raw page scrape CSS selectors into a CSV
file, or other easily-editable database, has failed. Some new edge case always
pops up to foil me.

Contrast this to all of the other REST API based interfaces I’ve worked with. I
can write a single wrapper class that easily translates JSON data from the API
endpoint to my own database format and table. Interfacing with Greenhouse, for
example, has lent itself to a CSV file that I can read once, and iterate through
to instantiate the appropriate API wrapper class.

Adding a new company to RTLjobs is a breeze if they're a Greenhouse customer:

- find their API token
- add that, their name, and their industry to the CSV file,
- you’re done!

The scraper reads in the CSV at runtime, and takes care of all the HTTP requests
and data reformatting for me, easy peasy.

Jon N. from NoWhiteboard deserves another shoutout here. This was also his idea.
If you’re a software engineer, and you’re sick of doing whiteboard interviews,
you really should [check out his site](https://nowhiteboard.org).

# It’s useful to grab all the site data - some of the time.

The first scraper I wrote didn't grab the entirety of a job post's HTML content.
This one fact from the first post inspired a ton of folks on HN to leave
feedback, mostly to the tune of: "this isn't that much data - why not grab it
all?"

So I figured, why not? I whipped up a proof of concept with `wget` and I can
confirm: yes! It’s _not_ that much data!

...if you're only doing one site.

I did a bit of scaling this to all the sites that RTLjobs.com monitors. With
daily updates, my SQLite3 database of raw post HTML approached 10GB after less
than a week of scraping. It's kind of large. Definitely too large to be a
production db any time soon.

For a new site or new interface, a whole site scrape can be a good thing. You
can scrape the whole site once, then test your CSS selectors/scraper extractors
on those posts. This reduces the overhead of hitting those sites over and over
to test your scraper. It's a handy way to tune your parameters without getting
your IP flagged, or your scraper tar pitted. I know `scrapy` has some options
that allow you to do this easily. I _still_ haven’t had the time to go deep on
`scrapy`, though.

For the time being, I’m sticking with my original plan of _not_ scraping and
storing site content. It has the benefits of:

- allowing me to work out of a single SQLite3 database file,
- not exceeding any file size limitations set by GitHub (they limit to 1GB for
  my tier of paid plan), and
- providing a natural slowing effect on the scraper, which helps my software
  remain polite.

# Learn to think like a search engine

When I wrote the last post, I was still using (abusing?) Selenium to draft off
of job sites' search pages. This is possible, but deeply frustrating: Selenium
ActionChains, while powerful, are a bit arcane. They also feel a lot like
specifying a lot of CSS selectors for job site parsing - it involves a lot of
manual coding I'd rather avoid. It’s also something that I’ve since learned is
bad form. Lots of robots.txt files include `/search` endpoints on their list of
disallowed URLs.

The brainwave came when I was in the shower, quietly wishing I had a search
engine at my disposal. Then it hit me: Why _don't_ I do this like a search
engine would? They're definitely not clicking thru a bunch of search pages. A
little research brought me to a goldmine of resources on an ancient bit of web 
tech: _sitemaps_. They ease so much of the pain of figuring out which pages 
are new to the scraper, and which are old. Learning to utilize a sitemap 
cut down my indexer's runtime from around three hours daily to under half an 
hour. They also did a bunch for helping me understand how Google indexes sites. 
If you're interested in content marketing, SEO, search engines, or any other 
stuff related to digital marketing: _spend a half day learning about sitemaps._ 
You absolutely will not regret it!

This research also yielded a bunch of other fruitful learnings about what websites do to
ease the plight of web indexing software. Adding a
[JSON JobPosting schema](https://schema.org/JobPosting) to the page head is
another thing I’ve come to appreciate. This is effectively a hack to get
JSON-readable data into webpages without going thru all the trouble of building
a JSON API. It doesn’t save you a ton of runtime; you still have to have your
crawler go thru every page and consume the JSON object. It does, however, save
you the trouble of writing a new set of CSS selectors for all of the in-page
data. The schema does the work for you of organizing all the data into an object
with the same keys, every time.

If you’re trying to hire, and you don’t have the resources to add a public API
to your job site, the JobPosting schema is your best bet for increasing the
search engine visibility of your posts. It can be easily adapted to new
webpages, and I’m willing to wager that someone has written a WordPress plugin
to add it without writing a single line of code. I’m also willing to bet that
your ATS supports this capability. Chat with your account manager about adding
this functionality! It really helps!

# Understand the levers that affect speed

My eventual goal for my scraping software is to get it running on a cloud
instance. Currently, it runs nightly on a spare Linux machine I have lying
around. Given that I want it to run in the cloud, I have a persistent incentive
to try and minimize the tuntime of the scraper. Cloud compute minutes ain't
free!

So far, the only thing I’ve found that really makes a difference for speeding up
raw scrapes is using the `requests.Session()` object. As best as I can tell,
this gives you a big boost in scraping speed by reusing the same TCP connection
for each new page. It’s not nearly as fast as `wget`, but it’s a big improvement
over `requests.get(url)`, which creates a new connection every time.

As an aside - good _gravy_ is `wget` fast. I was able to download about a
quarter of Intel’s ~10k available job postings in 30 seconds. This is so fast
that it will get you in trouble. Unconstrained `wget` calls to a file of URLs is
a one way trip to the tarpit for a live site.

# Next Steps on Job Scraping

Writing the scraping and indexing software on RTLjobs has been a _huge_
undertaking, but has yielded a ton of valuable insights for me. I’d estimate
that this software is about 80% of the way done. Remaining work is largely:

- add more software interfaces for other job board platforms (e.g. Workday,
  Taleo, ICIMS, etc), and
- add more employers to the board.

I’d also love to modularize the software a little more. RTLjobs is currently a
monolith. I can see how it might be useful to break it up into separate repos
for indexing software, web backend, and web frontend. Most of the value in doing
that would be forward looking; it would be a lot easier to extend the indexing
software I’ve written into other job domains if it existed as its own repo. It
would also be an opportunity to enhance testability of the backend and frontend.
This doesn’t have any immediately apparent ROI, as I’m the sole developer on the
project, and experience suggests to me that tests mostly provide value to teams
as a check for intended functionality. (I’m on twitter and I ain’t afraid of the
TDD crowd - fight me)

Using where I’ve spent my time on this project as a guide, it’s clear to me that
there are two main problems when it comes to job boards:

- Getting postings onto the board - either by scraping, or by getting users to
  add them,
- Making your board visually appealing, and easy to use.

I’ve talked a lot about scraping to get jobs on board. I’m long overdue in
discussing how to get users, or the work I’ve done on the user interface. Those
will come another day soon!
