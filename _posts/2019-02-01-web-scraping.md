---
title:  "Simple web scraping with Python"
date: 2019-02-01
permalink: /posts/2019/02/web-scraping/
categories: 
  - short
tags:
  - computational biology
  - python
  - bash
---

The situation: I wanted to extract chemical identifiers of a set of ~350 chemicals offered by a vendor to compare it to another list. Unfortunately, there is no catalog that neatly tabulates this information, but there is a product catalog pdf that has the list of product numbers. The detailed information of each product (including the chemical identifier) can be found in the vendor's website like this: `vendor.com/product/[product_no]`. Let me show you how to solve this problem with bash and Python.

Let's break the problem down into steps:
1. Extract list of product numbers (call it list A)
2. Iterate over list A and webscrape chemical id to get a list (call it list B)
3. Compare list B with desired list C

Steps 1 and 3 look easy -- just some text manipulation. Step 2 is basically the automated version of going to the product webpage and copy-paste the chemical identifier, and repeat this ~350 times (yup, not going to do that).

### Step 1
I have pdf catalogue that looks like this:

|Plate|Well	|Product| Product No.| 
|---	|---	|---	  |---	|
|1   	|A1  	|chemical x   	  |1111   	|
|1   	|A2 	|chemical y   	  |2222   	|
|... 	|... 	|...    |... 	|

And of course, when copy-pasted to a text file, it is messed up...
```
$ cat temp
1
A1
chemical x
1111
1
A2
chemical y
2222
...
```
Well, that is quite easy to fix. If we are sure that each table row becomes 4 lines, we can do some bash magic:
```bash
$ paste - - - - < temp > catalog.tsv
```
and we will get
```
$ cat catalog.tsv
1 A1  chemical x  1111
1 A2  chemical y  2222
...
```
But beware of empty cells! This may cause a table row to become fewer than 3 lines and mess up your data. This is why I choose `paste` in this case even though we could have just extracted every 4th line with `$ sed -n '0~4p' temp`. With a quick glance you can easily verify that the data is reformatted to look like the original table. 

So, inspecting that the reformatted table looks fine, extract the product number, i.e. the 4th column:
```bash
$ awk '{print $4}' catalog.tsv > prod_no.txt # list A
```

### Step 2
Let's do a test by scraping from the webpage of one product. Go to the webpage in your browser and do "Inspect element" to inspect the HTML underneath. I found my chemical identifier nicely contained in a `<div>` tag which has the id `inchiKey`.

Make sure you have the packages `requests` and `BeautifulSoup` and run this Python script:
```python
import requests
from bs4 import BeautifulSoup

prod_no = '1111'

def get_chemical_id():
    r  = requests.get("https://www.vendor.com/product/"+prod_no)
    data = r.text
    soup = BeautifulSoup(data, 'html.parser')
    inchi_key = soup.find('div', attrs={'id':'inchiKey'}).text
    print(inchi_key.rstrip())
    
get_chemical_id()
```
Do you get the correct chemical identifier? If so, it's time to wrap this in a loop that iterates over the list of product numbers:
```python
with open('prod_no.txt', 'r') as prod_no_list:
    for prod_no in prod_no_list:
        prod_no = prod_no.rstrip()
        print(prod_no, end='')
        get_chemical_id()
```
Together with the chemical id, I printed out the product number again to ensure correspondence -- some product numbers may be invalid and thus won't yield the chemical id! This is guarding against that. 

I get something like this as the output:
```
1111
    
    chemical id x

2222
    chemical id y
...
```
Notice the extraneous space and blank lines. Instead of trying to wrangle Python to output more consistently formatted output, I cleaned up with bash -- it's much easier:
```bash
$ sed 's/[[:space:]]//g; /^$/d' output > output_clean
```
You can confirm that each product number corresponds to a chemical identifier, then extracts just the identifiers like in Step 1:
```bash
$ paste - - < output_clean | awk '{print $4}' > chem_id.txt # list B
```

### Step 3
Easy:
```bash
comm     <(sort list_c.txt) <(sort list_b.txt) # C ∪ B
comm -12 <(sort list_c.txt) <(sort list_b.txt) # C ∩ B
```
`comm` outputs 3 columns: (1) C-B (2) B-C (3) C ∩ B. The flag `-12` suppresses columns 1 and 2. You can similarly suppress the other columns to output what you need.

### Bottom line
- Verify, verify your data at every step
- Freely switch bash and Python according to your needs
