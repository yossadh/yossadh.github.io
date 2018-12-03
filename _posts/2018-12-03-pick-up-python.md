---
title:  "Picking up Python as a scientist"
date: 2018-12-03
permalink: /posts/2018/12/pick-up-python/
categories: 
  - short
tags:
  - python
  - coding
---

My PhD supervisor prophetically decreed back in 2013 that "it might do thee some good to learn Python" (not his exact words). 

I did heed his advice and looked up some tutorials but soon abandoned it since it was not directly relevant to what I was doing. Now that Python is all the buzz, I feel a tinge of regret for not persisting to learn Python. I do believe the ability to code is now essential for a scientist regardless of fields, but especially in biology where we encounter growing size of data. This Nature article <[Programming: Pick up Python](https://www.nature.com/news/programming-pick-up-python-1.16833)> lays out this argument in more details with some resources for you to begin picking up Python.

<p align="center">
  <img src="/images/me_and_python.jpg" height="200px"/>
</p>
<p align="center">
Exhibit A: How much I love Python
</p>

I did pick up Python starting late last year, right after my thesis defense, while waiting for my work visa. While it is true that many resources and tutorials are out there, since Python is a popular language, I often found that the tutorials are more geared towards software developers. Scientists often do not need the full-blown Python capabilities or follow certain style guidelines, since we often write small scripts with fewer than 100 lines, instead of a big software.

So here I am listing some points and good practices I pick up from learning Python that are good to know, and mention some advanced features that you can skip from tutorials if you just want to pick up Python quickly. Some of the points are not Python-specific, but good coding practices and/or 'philosophy' of sorts (try googling [Zen of Python](http://wiki.c2.com/?PythonPhilosophy), for example). 

### Learning points for scientists from coding:
- Code documentation  
  I often opened my old script files and took a few minutes to figure out what on earth I was doing. For scripts, code comments are often enough for documentation ([here](https://realpython.com/python-comments-guide/) is a good commenting guide). But don't overcomment, e.g.:
  ```python
  # assign value to counter -> redundant comment
  counter = 0
  ```
  Furthermore, I think scientists can learn from the way software developers do their documentation, specifically applied to our lab notebook. One needs to deliberately devote time and effort to document. I will elaborate on lab notebooking in [another blogpost](TODO) (link will be updated).
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
  #do_thing_b # skip the whole function with line, instead of block, comment
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
    Not that important, since it can always be replaced by loop, but it is a powerful Python feature. It is more succinct and faster than loop. On the other hand, it can be too terse that readability suffers.
  - Packages: sys, sys.argv, os, math, numpy, pandas, matplotlib
  - Pick a good text editor/IDE (I use vi, Jupyter notebook, and VSCode)
  - *So far don't need*: assertion, try, exception, classes, decorator

Finally, here are some Python courses/tutorials I have tried:
- [edX \| Introduction to Computer Science and Programming Using Python](https://courses.edx.org/courses/course-v1:MITx+6.00.1x_7+3T2015/course/)  
  Comprehensive. Try this one if you need a solid foundation. Not suitable if you just want to get started quick.
- [edX \| Python for Data Science](https://courses.edx.org/courses/course-v1:UCSanDiegoX+DSE200x+2T2017/course/)  
  Introduces a lot of nice tools like Jupyter notebook and pandas.
- [Automate the Boring Stuff with Python](https://automatetheboringstuff.com/)  
  Practical stuff. Good for beginners who just want to pick up Python quickly.
- [DataCamp](https://www.datacamp.com/)  
  Bite-size step-by-step lessons. Good if you have only small chunks of time here and there. You will also be given a small exercise right after each lesson to make it stick.
- [Software Carpentry tutorials](https://software-carpentry.org/lessons/), also for UNIX shell and git  
- [How to think like a computer scientist](http://interactivepython.org/runestone/static/thinkcspy/index.html)  
  This one is my favourite. Things started to click for me here -- perhaps because I've done the other Python tutorials at this point, but give it a try.
