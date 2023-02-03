---
title: Converting Research Papers to Python
date: 2023-01-31 8:30:00 -500
categories: [AI, python]
tags: [AI,python,productivity]
---

I enjoy reading the research papers, but whenever I get to the mathematical formulas I find them tedious to read. I much prefer to have them in some format that I can change the variables to understand how each impacts the outcome. 

How can I convert formulas from research papers into python?

Most research papers are in PDF format, so the first step would be to convert them to a machine readable text format. But for the formulas themselves, this could get messy. 

# Converting PDF to text

There are a bunch of ways to do this using programs and python packages, but I used Mathpix. I'm not doing this all the time and they have a free tier which allows 20 'snips' and 20 pdf page conversions. 

Mathpix can convert the formulas within a PDF into Markdown. 

So as an example, let's say I want to convert the formulas from the 'Optimal Retirement Income Strategy' paper written by David Blanchett, which I recently [reviewed]:

![PDF_Formula_1](/assets/images/2023-01-31-PDFformula1.png)

![PDF_Formula_2](/assets/images/2023-01-31-PDFformula2.png)

The above two formulas get converted into Markdown:

```Markdown 
$$
N \ldots F R_{t}=\frac{A_{t}+\sum_{t=0}^{T} \frac{c p_{t}^{5} P_{t}}{\left(1+r_{p}\right)^{t}}}{\sum_{t=0}^{T} \frac{c p_{t}^{s} n-g_{t}}{\left(1+r_{n}\right)^{t}}}
$$

$$
W_{-} F R_{t}=\max \left(\frac{A_{t}+\sum_{t=0}^{T} \frac{c p_{t}^{s} P_{t}}{\left(1+r_{p}\right)^{t}}-\sum_{t=0}^{T} \frac{c p_{t}^{s} n-g_{t}}{\left(1+r_{n}\right)^{t}}}{\sum_{t=0}^{T} \frac{c p_{t}^{s} w_{-} g_{t}}{\left(1+r_{w}\right)^{t}}}, 0\right)
$$

```

This might look like jiberish, but when your browser reads this, it will convert the Markdown to look like this:

$$
N_{-} F R_{t}=\frac{A_{t}+\sum_{t=0}^{T} \frac{c p_{t}^{5} P_{t}}{\left(1+r_{p}\right)^{t}}}{\sum_{t=0}^{T} \frac{c p_{t}^{s} n-g_{t}}{\left(1+r_{n}\right)^{t}}}
$$

$$
W_{-} F R_{t}=\max \left(\frac{A_{t}+\sum_{t=0}^{T} \frac{c p_{t}^{s} P_{t}}{\left(1+r_{p}\right)^{t}}-\sum_{t=0}^{T} \frac{c p_{t}^{s} n-g_{t}}{\left(1+r_{n}\right)^{t}}}{\sum_{t=0}^{T} \frac{c p_{t}^{s} w_{-} g_{t}}{\left(1+r_{w}\right)^{t}}}, 0\right)
$$


This is meant to look the exactly like the original. The differece is that the Markdown version can be understood by a computer, which means that chatGPT will be able to understand it.  

## Converting Markdown into Python with chatGPT

Here I asked chatGPT to convert the markdown formula into a python code with the following prompt. Which then created two functions:

```python
def needs_funded_ratio(T, A, cp, P, ng, rp, rn):
    numerator = A + sum([cp[t]*P[t]/(1 + rp)**t for t in range(T+1)])
    denominator = sum([cp[t]*ng[t]/(1 + rn)**t for t in range(T+1)])
    NFR = numerator/denominator
    return NFR

def wants_funded_ratio(T, A, cp, P, ng, wg, rp, rn, rw):
    numerator = A + sum([cp[t]*P[t]/(1 + rp)**t for t in range(T+1)]) - sum([cp[t]*ng[t]/(1 + rn)**t for t in range(T+1)])
    denominator = sum([cp[t]*wg[t]/(1 + rw)**t for t in range(T+1)])
    WFR = max(numerator/denominator, 0)
    return WFR

```

From here it is a matter of defining the variables and running the functions. 




