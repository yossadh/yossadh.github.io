---
title:  "Formatting list of SMILES with bash scripting"
date: 2019-02-12
permalink: /posts/2019/02/format-smiles/
categories: 
  - short
tags:
  - bash
  - coding
---
Here is a fun text manipulation exercise using bash you can do in less than an hour.
Given that I have this text file, `file.smi`:
```
smiles1 some_id_abc
smiles2 some_id_xyz
...
```
I want to have it like this:
```
smiles1 C00000001
smiles2 C00000002
...
```
This was my real-world need of converting a list of SMILES to a format that is accepted by a conversion programme. Looks easy right? 

Here was what I did:
First I need to generate a sequence ending in total number of lines is `file.smi`:
```bash
# count lines in file.smi
wc -l < file.smi
# print sequence (1,2,3,...)
seq $(wc -l < file.smi)
# Add zero padding up to 8 characters (0000000x, 000000xx) 
seq -f "%08g" $(wc -l < file.smi)
# Add 'C' in the beginning (C0000000x, C000000xx) 
seq -f "%08g" $(wc -l < file.smi) | sed 's/^/C/' > new_ids_tempfile
```
Zero padding is the tricky part; the rest is just column manipulation:
```bash
# Put together smiles and new ids, change delimiter from tab to space
paste -d ' ' file.smi new_ids_tempfile
# file.smi may or may not have old ids, so just output first and last columns 
# to make sure only smiles and new ids are printed
paste -d ' ' file.smi new_ids_tempfile | awk '{print $1, $NF}'
```
Finally we can eliminate the intermediary tempfile by generating it on the fly:
```bash
paste -d ' ' file.smi <(seq -f "%08g" $(wc -l < file.smi) | sed 's/^/C/') | awk '{print $1, $NF}'
```
As with many one-liner text manipulation scripts, this is is pretty unintelligible at first glance. To do my future self a favour, I add extensive comments:
```bash
paste -d ' ' file.smi <(seq -f "%08g" $(wc -l < file.smi) | sed 's/^/C/') | awk '{print $1, $NF}'
#                                       |--count lines in file.smi         
#                       |--make sequences up to count, pad with zero up to 8 char width                
#                                                         |--add 'C' at beginning
#                     |--put the new ids C00000001,... in a temp file             
#|--paste together smiles and new ids, delimiter is single space                
#                                                                         |--finally, only output 
#                                                                            first and last columns
```

Hopefully this still makes sense in the future \\("▔□▔)/ \*cross fingers\* 

_Update_: I found a nice website ([explainshell.com](https://explainshell.com)) which can parse complicated one-liner shell commands like the above. Here is the [parsing](https://explainshell.com/explain?cmd=paste+-d+%27+%27+file.smi+%3C%28seq+-f+%22%2508g%22+%24%28wc+-l+%3C+file.smi%29+%7C+sed+%27s%2F%5E%2FC%2F%27%29+%7C+awk+%27%7Bprint+%241%2C+%24NF%7D%27) of the above.
