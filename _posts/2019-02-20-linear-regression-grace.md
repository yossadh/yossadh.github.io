---
title:  "Plotting business: Automated linear regression with Grace"
date: 2019-02-20
permalink: /posts/2019/02/linear-regression-grace/
categories: 
  - short
tags:
  - bash
  - grace
  - coding
---

What software do you use to plot? 

Like most people my first experience was with Excel. I remember the settings were quite confusing and not intuitive (this was Excel 2000/2003, no idea about current version). Then there are commercial softwares like Origin, which I used for here and there. It is quite user-friendly, but 1) I try to avoid pricy software packages when I can, and 2) commercial packages usually only run on Windows and I haven't had main Windows workstation in years. My experimental colleagues use mostly GraphPad, and the graphs look nice, but I never used it for the same reasons as Origin.

A comment about Google Sheets: one would expect it will be similar to Excel, but I'm pleasantly surprised that it is easy to use and yields pretty graphs. I use this for simple charts that do not need heavy customisation/annotations, e.g. histogram.

Most recently I have learned to use the myriad of Python packages: matplotlib, holoviews and the like. They are indeed powerful, pretty, and customisability is certainly there as far as your Python fu allows. But the learning curve is pretty high. Even when generating simple plots, I have to look up stuff again, even though I already have a previous script as template.

So, I found myself keep going back to Grace, an ancient(?) and arcane plotting software I first learned in undergraduate days when I first encountered a Linux machine. Ok I always thought it's ancient (like 70s-80s) because of how the GUI looks, but it was released in 1990s. Anyway, its graphs have a sort of signature look to it, and one can still see it in journal papers, especially molecular dynamics papers.

I can think of a few reasons why Grace sticks. First, you can get a plot very quickly from a plain-text file. In matplotlib, you have to import the data first into pandas dataframe and whatnot. Second, the plot file itself is a plain-text file containing the parameters and the data. Plain text means that it can be modified with the bash arsenal of text manipulation. Arguably, matplotlib is the same in this regard but it is a little different. The matplotlib `.py` script would be the _instructions_ to construct the graph, while the Grace `.agr` file have the parameters and their values there. For holoviews for example, I found myself looking up for the correct commands/instructions to set some parameters, while in Grace I can just go to to the parameter in question and change its value.

Here I will demonstrate how I use Grace and some tips and tricks. Suppose you have `data.dat` that you want to plot:
```bash
# if you don't have some data handy, use this random number generator
# generates 10 numbers less than 100
for i in {1..10}; do echo $i $(expr $RANDOM % 100); done > data.dat

# cat data.dat
1 11
2 3
3 29
4 87
5 15
6 38
7 6
8 55
9 7
10 32
```
Simply call Grace to plot it:
```bash
# Grace with GUI
xmgrace data.dat
```
And you will see a meh-looking plot. But the power of Grace is in the script automation. Save this plot as `data.agr`. Here is `fit.par` which will do linear regression and plot the regression plot:
```
# cat fit.par
with g0
view ymin 0.45
view ymax 0.85
s0 symbol 3
s0 symbol size 0.400000
s0 line type 0
# regression formula
fit formula "y = a0 + a1*x"
fit with 2 parameters
fit prec 1e-5
# run regression, 100 iterations
nonlfit(s0,100)
# duplicate data from original data (s0) to s1
copy s0 to s1
s1 symbol 0
s1 line type 1
s1 line color 7
s1 type xy
# overwrite y to make regression line
s1.y = a0 + a1*x
autoscale
```
Apply `fit.par`:
```bash
# Grace with command-line interface
grace data.agr -param fit.par -saveall data.fit.agr -hardcopy -noprint > data.fit.log
```
You will be glad when you have 15 plots to do linear regression on like I do. I guess you can do the clicking around on the GUI 15 times but what will you do if you have 100 plots?

One more thing I needed was to put the R<sup>2</sup> values in the graphs. I did this in a very roundabout way (please tell me if you have a more elegant solution):
- Annotate each of the 15 graphs (I have put them in one file, `plot.agr`) with textboxes containing the strings "corr1", ..., "corr15"
- Extract R-value from `data.fit.log` and calculate R<sup>2</sup>
  ```bash
  \rm correlation.dat
  for i in {1..15}; do
      grep -H Correlation data$i.fit.log >> correlation.dat
  done
  awk '{printf %.2f\n", $3^2}' correlation.dat > corr_squared.dat
- Make a dictionary `corr.dict` such that 'corr1' corresponds to first value of R<sup>2</sup>, and so on
  ```bash
  # cat corr.dict
  corr1 0.71
  ...
  corr15 0.80
  ```
- Replace `plot.agr` (the 15 graphs) consulting the dictionary (remember what I said about plain-text `.agr` being amenable to text manipulation?):
  ```bash
  awk 'NR == FNR {
    rep[$1] = $2
    next
  } 
  {
    for (key in rep)
      gsub(key, rep[key])
    print
  }' corr.dict plot.agr > plot_check.agr
  ```

Grace is powerful, but unfortunately the documentation, especially the scripting part, is a bit sparse. Hopefully that will improve!
