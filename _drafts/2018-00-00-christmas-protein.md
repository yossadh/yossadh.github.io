---
title:  "Searching for Christmas-sy proteins"
date: 2018-00-00
categories: 
  - short
tags:
  - computational biology
  - coding
---
I deal with protein sequences a lot for my work and once in a while it brings a smile on my face to see some intelligible words appearing amidst the seemingly randon letters. Since this is Advent, I wondered whether I can find some Christmassy words in the known protein universe!

This silly exercise will also show you a glimpse of data processing workflow commonly encountered in bioinformatics. Who says you can't learn something while having fun? ;)  

Note that this was done with Linux with local blastp (protein BLAST) installation.
{: .notice}

1. First let's source a list of words. A cursory Google search leads me [here](https://www.enchantedlearning.com/wordlist/christmas.shtml). Save this as xmas.txt
2. Do some clean up
```bash
# convert everything to lowercase
tr [:upper:] [:lower:] < xmas.txt > xmas.clean.txt 
# further cleanup 
sed -i '/[bgjouxz]/d      # delete all words containing non amino acid letters
        /^.$/d            # delete words with just one letter
        s/ //g            # delete single spaces
        s/[[:punct:]]//g  # remove punctuations
        ' xmas.clean.txt
```

3. Make FASTA file for each entry 
```bash
# add FASTA header
sed -i 's/^/>prot\n/' xmas.clean.txt
# split into individual files: xaa, xab, ...
split -l 2 xmas.clean.txt
``` 

4. Finally, submit them to blastp and wait a long time :D
```bash
for i in x??; do
    blastp -task blastp-short -remote -comp_based_stats 0 -query $i -db nr > $i.out
    done
```

