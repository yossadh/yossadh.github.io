---
title:  "Template Title"
date: 2019-01-00
permalink: /posts/2019/01/template-title/
categories: 
  - roundup
  - long
  - short
tags:
  - machine learning
  - science education
  - popular science
  - computational biology
  - python
  - bash
  - lab notebook
  - coding
---

Examples of recursive functions
```python
# factorial
def factorial(n):
    assert n>=0,"n has to be >=0"
    if n  > 1: return n*factorial(n-1)
    if n <= 1: return 1

for n in range(0,11):
    print(factorial(n), end=" ")
 
# fibonacci
def fibonacci(n):
    assert n>=0,"n has to be >=0"
    if n  > 1: return fibonacci(n-1)+fibonacci(n-2)
    if n == 1: return 1
    if n == 0: return 0

for n in range(0,11):
    print(fibonacci(n), end=" ")
```
