---
layout: post
title: "Grabbing Agilent Scope Screenshots in 10 Lines of Python"
date: 2014-03-28 14:28:56
---

I had the great good fortune to get a sweet new oscilloscope for work. An Agilent MSO-X 4104, if I may namedrop and brag a little bit. Four analog channels,
sixteen digital channels, 4Gs/sec @ 2GHz. Yeah. Pretty bitchin'. So why is it still a huge pain to get a screen capture for a report? 

I open the web portal. I right click the mouse. I click "Save As". Why go through all that stress?

OK, maybe I exaggerate. All that aside, this is a great use for Python in that it
 saves you a little time, and we all know that `time == money`.

{% highlight python %}
import requests
from bs4 import BeautifulSoup

BASE_URL = 'http://10.20.20.91'         # This is the IP address of the scope.
CAPTURE  = '/getImage.asp?inv=false'    # Screen capture URL

r = requests.get(BASE_URL+CAPTURE, stream=True)        # Request new screencap
for img in BeautifulSoup(r.content).find_all('img'):   # Find new screencap
    f_name = img['src'].split('/')[-1]
    target = requests.get(BASE_URL + img['src']).content # Image data
    with open(f_name, 'wb') as f:
        f.write(target).close()    # Grab, save, beer
{% endhighlight %}

`Time == money`. `Money == beer`. Stay thirsty, my friends.

[jekyll-gh]: https://github.com/mojombo/jekyll
[jekyll]:    http://jekyllrb.com
