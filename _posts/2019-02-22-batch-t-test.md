---
title:  "Doing t-test in batch"
date: 2019-02-22
permalink: /posts/2019/02/batch-t-test/
categories: 
  - short
tags:
  - python
  - coding
---
Situation: I want to do t-test between two sets of experiment A and B. Each A and B were run in triplicates: 
```
data_A_1.dat
data_A_2.dat
data_A_3.dat
data_B_1.dat
data_B_2.dat
data_B_3.dat
```
How to run t-test between every combination of A and B, as well as all A combined vs all B combined? 

Here is the script that I come up with:

```python
import numpy as np
from scipy import stats
import numpy as np
import os
import itertools
import sys
def printf(format, *args):
    sys.stdout.write(format % args)

os.chdir("/path/")

expt_A=[
"A_1",
"A_2",
"A_3",
]
expt_B=[
"B_1",
"B_2",
"B_3",
]

# load individual runs
data = dict()
for i in expt_A + expt_B:
    with open("data_"+ i +".dat", "r") as f:
        data[i] = np.array(f.read().splitlines()).astype(np.float)

# collate 3 runs
for i in ['A','B']:
    data[i+'_all']=np.concatenate([data[i+'_1'],
                                   data[i+'_2'],
                                   data[i+'_3']])

# print out t- and p-values
for i,j in list(itertools.product(expt_A, expt_B)) + [('A_all','B_all')]:
    print(i,j, end=": ")
    t, p = stats.ttest_ind(data[i],data[j])
    printf("t = %5.1f, p = %.2f\n", t, p)
```

The output looks like this (nicely formatted thanks to the `printf` trick):
```
A_1 B_1: t =  63.8, p = 0.00
A_1 B_2: t =  93.7, p = 0.00
A_1 B_3: t =  99.7, p = 0.00
A_2 B_1: t =  44.3, p = 0.00
A_2 B_2: t =  71.0, p = 0.00
A_2 B_3: t =  76.6, p = 0.00
A_3 B_1: t =  72.7, p = 0.00
A_3 B_2: t = 104.3, p = 0.00
A_3 B_3: t = 110.5, p = 0.00
A_123 B_123: t = 138.0, p = 0.00
```
