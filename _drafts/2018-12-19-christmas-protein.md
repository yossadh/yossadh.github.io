---
title:  "Searching for Christmas-sy proteins"
date: 2018-00-00
categories: 
  - short
tags:
  - computational biology
  - coding
---

I deal with protein sequences a lot for my work and once in a while it brings a smile on my face to see some intelligible words appearing amidst the seemingly random letters. Since this is Advent, I wondered whether I can find some Christmas-sy words in the known protein universe!

This silly exercise will also show you a glimpse of data processing workflow commonly encountered in bioinformatics. Who says you can't learn something while having fun? ;)  

**Note** that this was done with Linux with local blastp (protein BLAST) installation.

---

1. First, do a test case. Go to [NCBI blastp web interface](https://blast.ncbi.nlm.nih.gov/Blast.cgi?PROGRAM=blastp&PAGE_TYPE=BlastSearch&LINK_LOC=blasthome) and we will try to submit a peptide which has the sequence `CHRISTMAS`. Enter this in the query box:

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
   
   Hmmm, ok, so there is no `CHRISTMAS` sequence occurring yet in all the proteins that humans currently know! Very sad :(  
   To give you some idea of our search space, the protein database we are searching is nr, [RefSeq non-redundant protein sequences](https://www.ncbi.nlm.nih.gov/refseq/about/nonredundantproteins/), which as of 2018/11/22 has 178,521,967 sequences!  
   
1. Now let's repeat this with other Christmas-related words. Let's say you have around 100 words/sequences to BLAST, you wouldn't submit them one by one to the blastp webpage, would you? We will be using a local installation of blastp to do BLAST searches in batch. blastp is part of software suite BLAST+ made available by NCBI for that very purpose (see [here](https://blast.ncbi.nlm.nih.gov/Blast.cgi?PROGRAM=blastp&PAGE_TYPE=BlastSearch&LINK_LOC=blasthome) for more information).

1. Let's source a list of Christmas-related words. A cursory Google search leads me [here](https://www.enchantedlearning.com/wordlist/christmas.shtml). Copy-paste this to a text file. Make sure every word is in a separate line. Save this as `xmas.raw.txt`.

1. Do some clean up. This is easy to do in a Linux terminal. You can of course do manual clean up, but humans are inconsistent. It is better to automate the process with a script. The result will be consistent, traceable, easily customisable, and reproducible.

   ```bash
   # cleanup.sh: clean up list of christmas words for blastp search

   # convert everything to lowercase
   # note that we keep xmas.raw.txt untouched, for if we ever need to revisit the data cleanup again
   tr [:upper:] [:lower:] < xmas.raw.txt > xmas.clean.txt 
   # further cleanup 
   sed -i '/[bjouxz]/d       # delete all words containing non amino acid letters
           /^.$/d            # delete lines with just one letter
           s/ //g            # delete single spaces
           s/[[:punct:]]//g  # remove punctuations
           ' xmas.clean.txt
   ```

1. Make FASTA entry for each word 

   ```bash
   # add FASTA header
   sed -i 's/^/>prot\n/' xmas.clean.txt
   ``` 

1. Finally, it's BLAST time! From my experience, the web and local blastp sometimes give different results because of different parameters. To ensure consistency, you can save the search strategy from blastp webpage, thus capturing all the parameters. Save the search strategy file as `xmas.asn`. I further edited my search strategy file to point to my local protein database. 
Now before running on all words, do a test run to see if it gives you the same result as the webpage.

   ```bash
   blastp -import_search_strategy xmas.asn -out xmas.out
   ```
   
   All good? Go ahead and run on all words. My desktop took around 20 minutes. Perfect time to take _fikapaus_ :)
   
   ```bash
   # overwrite query in xmas.asn
   blastp -import_search_strategy xmas.asn -query xmas.clean.txt -out xmas.out    
   ```

1. *Jingle bells, jingle bells, oh what fun it is, to, ahem, analyse your result!*   
   Matches:
   `angel candle candy charity chill cider creche elves family festival garland greeting icicle kings lights manger merry mittens myrrh nativity navidad partridge presents reindeer scarf sleigh stnick sweater tidings tinsel wassail winter wintry wiseman wish wrap wreath` 
   
   No match:
   `chimney christmastide giftgiving iceskate mincepie santaselves santashelper santaslist` 
   
   Suprisingly no match:
   `cap card elf`
   
   And here are some near-matches, for your amusement:
   ```
   cannycane (candycane)
   emergreen (evergreen)
   fathprrhrlssmas (fatherchristmas)
   firpeplace (fireplace)
   fradkiksense (frankincense)
   mevarrlchrisim (merrychristmas)
   pvnetree (pinetree)
   sinterrlass (sinterklaas)
   widrertime (wintertime)
   wrarpigdgpaper (wrappingpaper)
   ```

   Just for fun, I also included `NATALIE`, our dear blog coach's name, in the search, and coincidentally her name is Christmas-sy! (Latin: *natalis dies Domini* = birthday of the Lord).
   Her name turns out to be quite popular across the kingdoms so to speak: from bacteria, fish, octopus, to birds, there are proteins with `NATALIE` inside :D   
   Here are some:
   ```
   PTV49966.1 hypothetical protein DBL04_17595, partial [Acinetobacter seifertii]
   PAA83426.1 hypothetical protein BOX15_Mlig003849g1 [Macrostomum lignano]
   XP_009469590.1 PREDICTED: dynein heavy chain 5, axonemal-like [Nipponia nippon]
   XP_010576315.1 PREDICTED: dynein heavy chain 5, axonemal-like [Haliaeetus leucocephalus]
   XP_009884215.1 PREDICTED: dynein heavy chain 5, axonemal-like [Charadrius vociferus]
   XP_021242631.1 dynein heavy chain 5, axonemal-like isoform X1 [Numida meleagris]
   ```

---

### Postscript
As I said in the beginning, there are some aspects of this exercise that can be applied to one's real-life project:
1. _Consistency_  
   You might notice that consistency is a motif in the exercise. I did a test case in blastp webpage first and compare the result to when I run local blastp. I wrote script for consistent data processing.
1. _Automate as much as possible, not only for ease but also reproducibility_  
   Not only that you should write scripts, you should pay attention to how you name your scripts and variables, as well as documenting them with comments. The future you will thank you! This really takes deliberate effort and I can tell you that I did not do enough of this during my PhD and I am committing to do more.

So with these Christmas-sy proteins, may you have a protein-packed Christmas. `MEVARRLCHRISIM`!
