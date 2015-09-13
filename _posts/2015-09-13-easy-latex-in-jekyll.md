---
layout: post
title: "Easily Add LaTeX Support To Jekyll"
date: 2015-09-13
---
In my own writing (and now with AllAboutCircuits) I've found myself using a ton of LaTeX for circuit equations. It gets the point across so much more nicely than notes scrawled on engineering paper, and looks clean and professional. (Plus, let's face it - my handwriting is chickenscratch on a good day.) I had a few sticking points getting it into my blog, but [this Stack Overflow post](http://stackoverflow.com/questions/10987992/using-mathjax-with-jekyll) got me on the right track. You just need to add the following line into your Jekyll post templates:

```html
<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>
```

Plop that into the top of your Jekyll post template, and your LaTeX equations will render nicely, like this subset of the wave equation derivation:

$$\frac{\delta E_{x}}{\delta t} = \frac{\delta f(z-ct)}{\delta t} = f^{\prime}(z - ct)\Big(\frac{\delta(z-ct)}{\delta t}\Big) = -c*f^{\prime}(z - ct)$$

$$\frac{\delta^2 E_{x}}{\delta t^2} = \frac{\delta}{\delta t} \Big(\frac{\delta Ex}{\delta t}\Big)= f^{\prime\prime}(z - ct)\Big(\frac{\delta(z-ct)}{\delta t}\Big) = c^2*f^{\prime\prime}(z - ct)$$

Here's the original LaTex, if you're interested in trying it out yourself:

```latex
\frac{\delta E_{x}}{\delta t} = \frac{\delta f(z-ct)}{\delta t} = f^{\prime}(z - ct)\Big(\frac{\delta(z-ct)}{\delta t}\Big) = -c*f^{\prime}(z - ct)

\frac{\delta^2 E_{x}}{\delta t^2} = \frac{\delta}{\delta t} \Big(\frac{\delta Ex}{\delta t}\Big)= f^{\prime\prime}(z - ct)\Big(\frac{\delta(z-ct)}{\delta t}\Big) = c^2*f^{\prime\prime}(z - ct)
```

Do that math, make that paper!