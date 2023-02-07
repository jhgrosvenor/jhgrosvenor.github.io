---
title: Converting Papers to Python
date: 2023-01-18 8:30:00 -500
categories: [AI, research]
tags: [AI,python,productivity]
math: true
---

I enjoy reading research papers, but I find the mathematical formulas tedious to read. I prefer to be able to change the variables to understand their impact on the outcome. I am seeking an efficient way to convert formulas from research papers into Python code.

Most research papers are in PDF format, so I need to first convert them to a machine-readable text format. Then, I plan to use OpenAI to translate the formulas into code.

# Converting PDF to text

There are many options for converting PDF documents to text, but I used [Mathpix](https://mathpix.com/). I'm not doing this all the time and they have a free tier which allows 20 'snips' and 20 pdf page conversions. 

I uploaded the research paper to [Mathpix](https://mathpix.com/), selected the pages containing the formulas and converted those pages to Markdown. 

As an example, let's say I want to convert the formulas from the '[Optimal Retirement Income Strategy](#references)' paper written by David Blanchett, which I'm currently reviewing:

![PDF_Formula_1](/assets/images/2023-01-31-PDFformula1Example.png)

![PDF_Formula_2](/assets/images/2023-01-31-PDFformula2Example.png)

[Mathpix](https://mathpix.com/) will convert the formulas into Markdown:

```Markdown 
$$
N \ldots F R_{t}=\frac{A_{t}+\sum_{t=0}^{T} \frac{c p_{t}^{5} P_{t}}{\left(1+r_{p}\right)^{t}}}{\sum_{t=0}^{T} \frac{c p_{t}^{s} n-g_{t}}{\left(1+r_{n}\right)^{t}}}
$$

$$
W_{-} F R_{t}=\max \left(\frac{A_{t}+\sum_{t=0}^{T} \frac{c p_{t}^{s} P_{t}}{\left(1+r_{p}\right)^{t}}-\sum_{t=0}^{T} \frac{c p_{t}^{s} n-g_{t}}{\left(1+r_{n}\right)^{t}}}{\sum_{t=0}^{T} \frac{c p_{t}^{s} w_{-} g_{t}}{\left(1+r_{w}\right)^{t}}}, 0\right)
$$
```

This might look like gibberish, but when your browser reads this, it will convert the Markdown to look like this:

$$
N_{-} F R_{t}=\frac{A_{t}+\sum_{t=0}^{T} \frac{c p_{t}^{5} P_{t}}{\left(1+r_{p}\right)^{t}}}{\sum_{t=0}^{T} \frac{c p_{t}^{s} n-g_{t}}{\left(1+r_{n}\right)^{t}}}
$$

$$
W_{-} F R_{t}=\max \left(\frac{A_{t}+\sum_{t=0}^{T} \frac{c p_{t}^{s} P_{t}}{\left(1+r_{p}\right)^{t}}-\sum_{t=0}^{T} \frac{c p_{t}^{s} n-g_{t}}{\left(1+r_{n}\right)^{t}}}{\sum_{t=0}^{T} \frac{c p_{t}^{s} w_{-} g_{t}}{\left(1+r_{w}\right)^{t}}}, 0\right)
$$


This is meant to look the same as the picture. 

Now the formula is written in Markdown, it can be provided to chatGPT to understand it and convert it to a python program.  

## Converting Markdown to Python with chatGPT

Here I asked chatGPT to convert the markdown formula into python code, which then created two functions:

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

Two important things to keep in mind with this method:

1) Thorough testing is crucial to ensure the code functions correctly. Some coding knowledge helps with troubleshooting, but I have also found ChatGPT to be effective in error resolution.

2) ChatGPT may not understand the context if the functions are actually sub-functions of another function. In this case, you need to step through the formula from the beginning.

In the examples above the the needs and wants funded ratio are inputs into another fomrula. Therefore the 't in range(T+1)' would need to be altered. 

## References
Blanchett, David. "[Redefining the Optimal Retirement Income Strategy.](https://www.tandfonline.com/doi/pdf/10.1080/0015198X.2022.2129947?needAccess=true&role=button)" Financial Analysts Journal 79, no. 1 (2023): 5-16. 

