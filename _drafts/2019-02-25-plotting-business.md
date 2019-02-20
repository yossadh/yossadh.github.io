---
title:  "Plotting business"
date: 2019-02-25
permalink: /posts/2019/02/plotting-business/
categories: 
  - short
tags:
  - bash
  - coding
---

What software do you use to plot? 

Like most people my first experience was with Excel. I remember the settings were quite confusing and not intuitive (this was Excel 2000/2003, no idea about current version). Then there are commercial softwares like Origin, which I used for here and there. It is quite user-friendly, but 1) I try to avoid pricy software packages when I can, and 2) commercial packages usually only run on Windows and I haven't had main Windows workstation in years. My experimental colleagues use mostly GraphPad, and the graphs look nice, but I never used it for the same reasons as Origin.

Most recently I have learned to use the myriad of Python packages: matplotlib, holoviews and the like. They are indeed powerful, pretty, and customisability is certainly there as far as your Python fu allows. But the learning curve is pretty high. Even when generating simple plots, I have to look up stuff again, even though I already have a previous script as template.

So, I found myself keep going back to Grace, an ancient(?) and arcane plotting software I first learned in undergraduate days when I first encountered a Linux machine. Ok I always thought it's ancient (like 70s-80s) because of how the GUI looks, but it was released in 1990s. Anyway, its graphs have a sort of signature look to it, and one can still see it in journal papers, especially molecular dynamics papers.

I can think of a few reasons why Grace sticks. First, you can get a plot very quickly from a plain-text file. In matplotlib, you have to import the data first into pandas dataframe and whatnot. Second, the plot file itself is a plain-text file containing the parameters and the data. Plain text means that it can be modified with the bash arsenal of text manipulation. Arguably, matplotlib is the same in this regard but it is a little different. The matplotlib `.py` script would be the _instructions_ to construct the graph, while the Grace `.agr` file have the parameters and their values there. For holoviews for example, I found myself looking up for the correct commands/instructions to set some parameters, while in Grace I can just go to to the parameter in question and change its value.

Here I will demonsrate how I use Grace and some tips and tricks.


A comment about Google Sheets: one would expect it will be similar to Excel, but I'm pleasantly surprised that it is easy to use and yields pretty graphs. I use this for simple charts that do not need heavy customisation/annotations, e.g. histogram.

For me, these are the ones I have used:
- Excel: confusing settings?
- Google Sheets: actually looks decent
- Python packages (matplotlib and holoviews): pretty and very heavily customisable with Python scripting
- grace: more details below 
