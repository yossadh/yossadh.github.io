---
title:  "PyMol Python API"
date: 2018-00-00
categories: 
  - short
tags:
  - computational biology
  - python
  - coding
---
I almost exclusively used VMD during my PhD and was aware of the other two popular molecular visualisation softwares PyMol and Chimera, but only hardly used them. One reason is that VMD is very much suited for molecular dynamics visualisation. The other reason is just inertia, I guess, one just keeps using what one is familiar with.

However, now I find myself use PyMol almost exclusively with occasional use of Chimera. Even though the PyMol GUI is not that intuitive and takes some time to get used to, it's smooth after you are familiar with it. Also, the fact that you can tell it to do stuff in Python is powerful, and this is what this post is about. Chimera, too, has Python API and I do use it when PyMol cannot do certain things, but PyMol's Python API is better documented. VMD, too, is extensible with Python and Tcl, but again, documentation is a bit sparse (or I just didn't frequent the mailing list that much).

Now, you don't need Python to do some automated tasks in PyMol. You can put all the commands that you would type in PyMol terminal in a .pml file, like so:
'''
# setup.pml
bg white   # set background colour to white
fetch 1dt9 # load PDB structure 1DT9 from PDB website
'''
If you open `setup.pml` with PyMol it will execute the two commands as you would expect.

Now if you use the Python API, the script would be:
'''python
# setup.pymol.py
import pymol
pymol.finish_launching()
pymol.cmd("bg white")   # set background colour to white
pymol.cmd("fetch 1dt9") # load PDB structure 1DT9 from PDB website
'''
You can run this directly with Python: `python setup.pymol.py`
Or, with PyMol: `pymol setup.pymol.py`
And you will get the same result as the previous case.

Why use the Python API, then, if it is more verbose? Well, with Python API you get access to full Python functionalities like the conditionals and loops, and also Python objects like lists and dictionaries.
 
