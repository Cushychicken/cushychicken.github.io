---
layout: post
title: "Creating dynamic mailto: links in Flask"
summary: "you've got mail"
tags: ['programming']
date: 2021-11-30

---

I've spent the last month working on [www.rtljobs.com](https://www.rtljobs.com), a niche job aggregator for logic design jobs. Our first scraping pipeline had some warts, so I wanted to be sure that the site had was a way to report bad job links. That way, if a user clicked on a job link that was no longer active, they could alert me that one of the job postings was taken down. 

The simplest way I could think of doing this was thru email. I’ve always had a fondness for `mailto:` links, so I thought; why not use that here? Here’s what I came up with in my Flask application that runs RTLjobs.com: 

```python
def generate_mailto(url, recipient='fpga.rtl.jobs@gmail.com', subject="[Report] - Broken Link"):
    body = f'''The following URL is having problems and may be broken:

{url}

Please investigate. Thank you!'''

    body = urllib.parse.quote_plus(body)
    mailto = 'mailto:?to='+recipient+'&subject='+subject+'&body='+body
    return(mailto)

app.jinja_env.globals.update(generate_mailto=generate_mailto)
```

That last line adds `generate_mailto()` into Jinja2's global namespace. 

This way, you can call your function directly from a template, like this:

```
<a href="{{ '{{' }} generate_mailto(row['job_link']) }}">Report This Posting</a>
```

Which allows a user to click one button to generate a notification email to me, with the problematic URL already included. 

This was a useful hack to help me crowdsource some feedback in the first weeks that [RTLjobs](https://www.rtljobs.com) was active. I’ve replaced it with some more sophisticated link health monitoring, but there’s no reason you can’t adapt this trick to other applications where a mailto: link might come in handy! 
