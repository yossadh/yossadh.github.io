---
title:  "A guide to PyMol Python API"
date: 2019-02-15
permalink: /posts/2019/02/pymol-python-api/
categories: 
  - short
tags:
  - python
---

I used to use VMD for all my molecular visualisation needs but I have become a PyMol convert as of late. Even though both are extensible with scripting, I find VMD harder with Tcl which is unfamiliar to me, while with PyMol I can use Python, which I am already familiar with.

First off, if you just need simple stuff like loading some molecules, then colour them and do something, you can just use a `.pml` file, which is the equivalent of inputting the lines in the `.pml` file to the PyMol command line terminal. 

Here is one example of loading many molecules, then colour them according to their types:
```
# load.pml
load 1.a
load 1.b
load 1.c
load 1.d
load 2.a
load 2.b
load 2.c
load 2.d
color black, *.a
color green, *.b
color yellow, *.c
color red, *.d
```

Here is another example -- load a trajectory and make a movie: 
```
# traj.pml
load start.gro, traj
load_traj traj.xtc, traj
mset 1x500
mview store, 1, state=1, object=traj
mview store, 500, state=500, object=traj
intra_fit traj
smooth
```

So far quite simple right? Now, if you need loops, variables, lists, dictionaries, conditional statements, and so on, then it's Python time!

You need to communicate to PyMol with Python. Do the `import`, alias the PyMol commands to a shorter command (here I use `pm=pymol.cmd`), and set up the initial PyMol environment settings:    
```python
# load.py
def main():
    pm.load("start.gro", "traj")
    
####################################################
import __main__
__main__.pymol_argv = ['pymol','-qc']
import pymol
import os
import sys
import os.path
import numpy as np
from time import sleep
import glob

pymol.finish_launching()
pm=pymol.cmd

pm.window('maximize')
pm.set("internal_gui_width", "546")
pm.set('bg_rgb',0,'',0) # white background
pm.set("sphere_scale", "0.4")
pm.set("line_width", "2")
pm.set("ribbon_side_chain_helper", "on")
pm.set('orthoscopic',1,'',0)
pm.set("group_auto_mode", 0)
pm.set("ray_opaque_background", 0)

main()
```

Notice the difference between the `load` commands in `load.pml` and `load.py`: 
```
load start.gro, traj
``` 
will become 
```
pm.load("start.gro", "traj")
```
Note the quote marks, which means they are Python strings. So one can think of it as if `pm.load` just passes the two strings to the real `load` command. That's what 'interface' in API means.

I am aware that I'm violating PEP8 by putting stuff before `import` statements. Well, I don't give a, you know. But this is not mere caprice:
- This is a short script not a big software project.
- This an API call, the `import` statements and initial settings will not change, mostly, so I will not touch this part of the script again.
- I often have to do remote ssh connection and it's not always smooth, so it's important that I can open the script and get to the main logic quickly.


capture output
