---
layout: post
title: "Scraping Practice Material in Python"
date: 2014-06-02    
---

I have a love bordering on obsession with steel guitars; my latest musical
endeavour is learning the pedal steel guitar. If you're not familiar, it's
easier to show rather than tell - here's [the great Buddy
Emmons](https://www.youtube.com/watch?v=21TI7vlOdh4) accompanying Ray Price on
"Night Life". Buddy's the guy in the bowler hat sitting behind the thing that
looks like a table; that table is the pedal steel. It's a great instrument, but
supremely complicated to learn - in no large part due to the lack of teaching
material available. That's why I was so pumped to find [Greg Cutshaw's steel
guitar page](http://www.gregcutshaw.com/). Greg's been kind enough to put
together tab of all the great steel guitar riffs he's written over the years. On
top of that, he put them up on his website - for free! Spend a few hours combing
Amazon for "C6 pedal steel instruction" and you'll see why this is such a gold
mine!

For all of the great info on Greg's page, however, the UX leaves something to be
desired. Navigation, generally, is a bit of a headache.  Each page has linked
JPGs or PDFs, each with its own (rarely consistent) naming scheme. I'd like to
be able to flip through these in the Finder on my Mac, maybe print them out into
a book, or (if I get really ambitious) arrange them into an Anki set with audio
samples. To do any of that, however, you first need to grab the images - all 25
pages of them, with ~50 tabs per page. Who wants to do that by hand when we've
got Python to do it for us?

<div align="center">
<img src="/assets/cutshawsite.png"/>
<p align="center"><em>You're bound to see some Web 1.0 when searching for pedal
steel tab.</em></p>
</div>

Some quick digging into the source shows that the code's arranged pretty simply
- just a big HTML table. I whipped up the following scraping function using
BeautifulSoup to go through and pull out the title of each of Greg's tabs, plus
the path of the tab file. I sprinkled in a dash of exception handling to deal
with any possible formatting foibles along the way.

{% highlight python %}
from bs4 import BeautifulSoup
import re

pdf_re = re.compile(r'.*(\.pdf|\.jpg)') # Quick regex to check for file

def tabscrape(pagetext):
    tempdata = []
    soup = BeautifulSoup(pagetext)
    for tr in soup.find_all('tr')[1:]:
        try:
            td = tr.find_all('td')
            if pdf_re.search(td[1].a['href']):
                tmp = [ td[0].text.strip(), td[1].a['href'] ]
                tempdata.append(tmp)
        except Exception as e:
            print tr
            print e
            pass
    return tempdata
{% endhighlight %}
        
The URL structure of the index pages isn't hard to figure out - just a sequence
starting from `Tab2.html` and ending at `Tab24.html`. Iterate over that range,
make a few HTTP requests, and you've got a nice big data structure with all of
the tab names and files at your disposal!

{% highlight python %}
import requests

BASE_URL = 'http://www.gregcutshaw.com/Tab/'
INDEX_URL = 'Tab%d.html'

alltabs = []
for a in range(2, 24):
    r = requests.get(BASE_URL + (INDEX_URL % a))
    print 'Scraping page %d...' % a
    pagetabs = tabscrape(r.text)
    alltabs = alltabs + pagetabs
{% endhighlight %}
    
Now that we've got a fat chunk of URLs and their names, it's reasonably 
simple to download all of the content to a local directory. It's important 
to check for a successful HTTP request - I found out that a few
of the links were broken when they crashed and threw 404 errors. Even with
those, however, it's probably the quickest way I know of to net about 400 
free pedal steel practice tabs, as well as a fun way to sharpen some Python 
skills.

{% highlight python %}
for tab in alltabs:
    result = pdf_re.search(tab[1])
    r = requests.get(BASE_URL + tab[1], stream=True)
    if r.status_code == 200:
        with open(tab[0] + result.group(1), "wb") as f: 
            for block in r.iter_content(1024):
                if not block:
                    break
                f.write(block)
{% endhighlight %}
                
A big thanks to Greg Cutshaw for all of his work assembling this material 
over the years. If you play pedal steel but aren't a Pythonista, feel free 
to get in touch if you'd like all of the steel tabs as a .zip file!

