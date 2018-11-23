---
title:  "Searching for Christmas-sy proteins"
date: 2018-00-00
categories: 
  - short
tags:
  - computational biology
  - coding
---
I deal with protein sequences a lot for my work and once in a while it brings a smile on my face to see some intelligible words appearing amidst the seemingly randon letters. Since this is Advent, I wondered whether I can find some Christmas-sy words in the known protein universe!

This silly exercise will also show you a glimpse of data processing workflow commonly encountered in bioinformatics. Who says you can't learn something while having fun? ;)  

**Note** that this was done with Linux with local blastp (protein BLAST) installation.

* First, do a test case. Go to [NCBI blastp web interface](https://blast.ncbi.nlm.nih.gov/Blast.cgi?PROGRAM=blastp&PAGE_TYPE=BlastSearch&LINK_LOC=blasthome) ans we will try to submit a peptide which has the sequence `CHRISTMAS`. Enter this in the query box:

```
>prot
CHRISTMAS
```

And let's see the top 3 results...

```
Query  1    CHRISTMAS  9
            CHRI TM S
Sbjct  313  CHRITTMSS  321

Query  1   CHR---ISTMAS  9
           CHR   ISTMAS
Sbjct  12  CHRLEKISTMAS  23

Query  1    CHRISTMAS  9
            CHR+S MAS
Sbjct  97   CHRVSSMAS  105
```

Ok, so there is no `CHRISTMAS` sequence occurring yet in all the proteins that humans currently know! Very sad :(

* Now let's repeat this with other Christmas-related words. Let's say you have around 100 words/sequences to BLAST, you wouldn't do this one by one to the blastp webpage, would you? We will be using a local installation of blastp to do BLAST searches in batch. blastp is part of software suite BLAST+ made available by NCBI for that very purpose (see [here](https://blast.ncbi.nlm.nih.gov/Blast.cgi?PROGRAM=blastp&PAGE_TYPE=BlastSearch&LINK_LOC=blasthome) for more information).

* Let's source a list of Christmas-related words. A cursory Google search leads me [here](https://www.enchantedlearning.com/wordlist/christmas.shtml). Copy-paste this to a text file. Make sure every word in in a separate line. Save this as `xmas.txt`.

* Do some clean up. This is easy to do in a Linux terminal. You can of course do manual clean up, but humans are inconsistent. It is better to automate the process with a script. The result will be consistent, traceable, and reproducible.

```bash
# convert everything to lowercase
tr [:upper:] [:lower:] < xmas.txt > xmas.clean.txt 
# further cleanup 
sed -i '/[bgjouxz]/d      # delete all words containing non amino acid letters
        /^.$/d            # delete lines with just one letter
        s/ //g            # delete single spaces
        s/[[:punct:]]//g  # remove punctuations
        ' xmas.clean.txt
```

* Make FASTA file for each entry 

```bash
# add FASTA header
sed -i 's/^/>prot\n/' xmas.clean.txt
# split into individual files with fasta extension
split -l 2 --additional-suffix='.fasta' xmas.clean.txt
``` 

* Finally, it's BLAST time! From my experience, the web and local blastp sometimes give different results because of different parameters. To ensure consistency, you can save the search strategy from blastp webpage, thus capturing all the parameters. Save the search strategy file as `christmas.asn`. I further edited my search strategy file to point to my local protein database. 
Now before running on all words, do a test case to see if it gives you the same result as the webpage.

```bash
blastp -import_search_strategy christmas.asn -out christmas.out
```

All is good? Go ahead and run on all words. It took ~9 hours for me. Best to run it overnight :)
```bash
for i in *.fasta; do
    blastp -import_search_strategy christmas.asn -query $i -out $i.out    
done
```
* Jingle bells, jingle bells, oh what fun it is, to analyse your result! 
Just for fun, I also included `NATALIE`, our dear blog coach in the search, and coincidentally her name is Christmas-sy! (Latin: *natalis dies Domini* = birthday of the Lord)
Her name turns out to be quite popular across the kingdoms so to speak, from bacteria, fish, octopus, to birds, there are proteins with `NATALIE` inside :D

And here are some near-matches, for your amusement:
- cannycane (candycane)

Lessons to apply to real-life project:
- Do test cases
- Automate as much as possible, not only for ease but also reproducibility
