---
layout: post
title: "Errbody do what errbody do."
date: 2014-04-14 23:14:41
---

You'll find this post in your `_posts` directory - edit this post and re-build (or run with the `-w` switch) to see your changes!
To add new posts, simply add a file in the `_posts` directory that follows the convention: YYYY-MM-DD-name-of-post.ext.

Here I am, on a plane, en route to Shanghai by way of Tokyo. 

{% highlight python %}
import requests
from bs4 import BeautifulSoup

BASE_URL = 'http://10.20.20.91'
CAPTURE  = '/getImage.asp?inv=false'

r = requests.get(BASE_URL+CAPTURE, stream=True)
for img in BeautifulSoup(r.content).find_all('img'):
    f_name = img['src'].split('/')[-1]
    with open(f_name, 'wb') as f:
        f.write(requests.get(BASE_URL + img['src']).content)
        f.close()
{% endhighlight %}

Check out the [Jekyll docs][jekyll] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll's GitHub repo][jekyll-gh].

[jekyll-gh]: https://github.com/mojombo/jekyll
[jekyll]:    http://jekyllrb.com
