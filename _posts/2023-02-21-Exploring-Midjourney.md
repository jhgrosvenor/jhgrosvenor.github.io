---
title: Exploring Midjourney
date: 2023-02-21 8:30:00 -500
categories: [AI, research]
tags: [AI,image,generation]
---
## What is MidJourney?
[MidJourney](https://www.midjourney.com) is an AI image generator that utilizes machine learning algorithms to produce highly realistic images of various objects, scenes, and landscapes. MidJourney is capable of generating stunning visual content by learning from vast amounts of data and then applying that knowledge to generate new, unique images.

## Creating from an image
Aside from creating images from scratch, MidJourney can also take images and along with prompts to create new images. 

Let us start with my LinkedIn profile photo. 

![Joel](/assets/images/2023-02-21/2023-02-21-Joel.jpeg){: width="400" height="400" }

MidJourney requires at least one text prompt, if the prompt isn't specific you'll receive very random results.

```
/imagine prompt: <photo link> ?
```
![Imagine1](/assets/images/2023-02-21/2023-02-21-imagine1.png){: width="400" height="400" }

I'm trying not to take bird face one personally. 

Let us try something more specific.

```
/imagine prompt: <photo link> 80 year old man
```
![Imagine2](/assets/images/2023-02-21/2023-02-21-imagine2.png){: width="400" height="400" }

It's pretty impressive results from a very short and non-descriptive prompt. Now let's add some more specific words to the prompt to improve the output quality. 

```
/imagine prompt: <photo link> 80 year old natural light, Hyper Realistic Photography, Hyperdetail, Ultrahd, hdr, color grading, 8k
```
![Imagine3](/assets/images/2023-02-21/2023-02-21-imagine3.png){: width="400" height="400" }

From here we can select one of the four panels to upscale into a more detailed image. I reckon the top left panel is probably what I'll look like, provided I don't change my hair style.

![Imagine4](/assets/images/2023-02-21/2023-02-21-imagine4.png){: width="400" height="400" }

## Limitations
While MidJourney and other AI image generators have shown remarkable progress in creating visually stunning graphics and artworks, the idea that they will completely replace human graphic designers seems unrealistic at this stage. There are still many issues to address with this sort of tech:

- Minor changes a major problem. 
 ```
/imagine prompt bird in blue hat
 ``` 
![Imagine5](/assets/images/2023-02-21/2023-02-21-imagine5.png){: width="400" height="400" }
 
 Here I used the original image as the 

 ```
/imagine prompt bird in red hat --seed 257951768 
 ``` 
![Imagine6](/assets/images/2023-02-21/2023-02-21-imagine6.png){: width="400" height="400" }

- Can't handle text. 
```
/imagine prompt sign saying hello world
```
![Imagine7](/assets/images/2023-02-21/2023-02-21-imagine7.png){: width="400" height="400" }

- Can't handle some things, hands for example. 
Whenever you see an image created by AI, it's often the finer details which will look strange. An arm bending multiple times or a hand with no five extra-long fingers. It does some things very well, but hands are not one of them. 
```
/imagine prompt two kids high fiving in a playground photo Hyper Realistic Photography, Hyperdetail, Ultrahd, hdr, color grading, 8k 
```

![Imagine8](/assets/images/2023-02-21/2023-02-21-imagine8.png){: width="400" height="400" }

- Image quality.
MidJourney produces rastered images rather than vectors, which creates issues when trying to enlarge the image. Resolution is is a limitation to how large the images can become. This isn't so much of an issue when viewing on a computer, tablet or phone. But this is a serious limitation to printing images for real world applications. Of course there are work arounds, but until all of the above are integrated into a single fool proof solution, it's likely there will be a role for someone else with expertise to step in an assist. 

## Final Thoughts
So unless we're just going to live with these issues just so we can get rid of all graphic designers, it's likely that they're going to stick around for some time. 

My view is that advancements in this area are likely going to form tools for graphic designers, rather than replace them. There will still be the need for creative minds and people who can draw hands. 



