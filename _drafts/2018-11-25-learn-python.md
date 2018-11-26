---
title:  "Picking up Python as a scientist"
date: 2018-xx-xx
categories: 
  - short
tags:
  - python
  - coding
---

My PhD supervisor prophetically said to me back in 2013 to learn Python. I did heed his advice and looked up some tutorials but soon abandoned it since it was not directly relevant to what I was doing. Now that Python is all the buzz, I feel a tinge of regret for not persisting to learn Python. I do believe the ability to code is now essential for a scientist regardless of fields, but especially in biology where we encounter growing size of data. This Nature article <[Programming: Pick up Python](https://www.nature.com/news/programming-pick-up-python-1.16833)> lays out this argument in more details with some resources for you to begin picking up Python.

I did pick up Python starting late last year, right after my thesis defense, while waiting for my work visa. While it is true that many resources and tutorials are out there, since Python is a popular language, I often found that the tutorials are more geared towards software developers. Scientists often do not need the full-blown Python capabilities or follow certain style guidelines, since we often write small scripts with fewer than 100 lines, instead of a big software.

So here I am listing some points that I pick up from learning Python, and mention some features that you probably do not need, but of course you can always find out more if you need them. Some of the points are not Python-specific, but good coding practices and/or 'philosophy' of sorts (try googling [Zen of Python](http://wiki.c2.com/?PythonPhilosophy), for example). 

### Learning points for scientists from coding:
- Code documentation  
  I often opened my old script files and took a few minutes to figure out what on earth I was doing. For scripts, code comments are often enough for documentation. But don't overcomment, e.g.:
  ```python
  # assign value to counter -> redundant comment
  counter = 0
  ```
- Code review and refactoring  
  This is related to the previous point. If you write readable and well-structured code, sometimes comments become unnecessary. Code has to be revisited and revised, not only for the logic, but for structure.
- Naming files, variables, functions  
  Related to code readability. Variable name `user_id` is self-explanatory compared to `x`. 
- Dynamic typing; object types: int, float, str  
- Conditional and loop statements  
  Often these make up the bulk of the logic that you need from a script, so be sure to know them well.
- Functions and abstractions  
  View your script in modular fashion: break them to steps and tuck each step in a function. This way of thinking is powerful to solve a big problem by breaking it down to manageable smaller problems.
  ```bash
  # bash script
    
  # the inner workings of do_thing_a and do_thing_b are not shown (abstracted): 
  # - overall logical structure becomes clear
  # - easy to comment out
  main(){
  do_thing_a
  do_thing_b
  }
  
  do_thing_a(){
  ...
  }
  
  do_thing_b(){
  ...
  }
  
  main
  ```
- File I/O (opening, reading, and writing file)
- Python-specific:
  - Object types: list, tuple, set, dictionary
  - List comprehension  
    Not that important, since it can always be replaced by loop, but it is a powerful Python feature and faster than loop
  - Packages: sys, os, math, numpy
  - Use text editor/IDE with syntax highlighting (I use vi and Jupyter notebook)
  - *So far don't need*: assertion, exception

My own favourite Python tutorials:
- [Software Carpentry tutorials](https://software-carpentry.org/lessons/), also for UNIX shell and git  
- [How to think like a computer scientist](http://interactivepython.org/runestone/static/thinkcspy/index.html) 

Related posts: Jupyter notebook
