---
title:  "Splitting a row of matplotlib subplots to columns"
date: 2021-10-25
permalink: /posts/2021/10/matplotlib/
categories: 
  - short
tags:
  - python
---
Case: I have many rows of subplots and would like to divide them up in multiple columns  

The solution is quite simple with modulo operator, although it took me embarassingly sometime to figure it out.  
First, let's say we have a list of plot names:
```python
plots = ['plot1', 'plot2',...]
```
If we plot this in one column, it would be:
```python
ncol=1
nrow=len(plots)

fig, axes = plt.subplots(nrow, ncol)

for ind, title in enumerate(plots):
    ax = axes[ind]
    
    ...plt(ax=ax)
```
Now if we want to split this to, say, 3 columns,
```python
ncol=3
nrow=int(len(plots)/ncol)

fig, axes = plt.subplots(nrow, ncol)

for ind, title in enumerate(plots):
    ax = axes[ind%nrow,int(ind/nrow)]
    
    ...plt(ax=ax)
```
For the row index, the modulo operator 'resets' `ind` for every column by modulo-ing it with `nrow`.
For the column index, simply divide `ind` with `nrow` and take the integer.
