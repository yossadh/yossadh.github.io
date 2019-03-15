---
title:  "Extracting sequence from PDB file"
date: 2019-03-15
permalink: /posts/2019/03/extract-pdb-sequence/
categories: 
  - short
tags:
  - bash
---
There are times that you need the sequence of the _resolved_ amino acids in an X-ray crystal structure, not the full sequence of the construct. If you download the FASTA sequence or check the `SEQRES` record in the PDB file, you would find the full sequence.

Of course, extracting sequence is easy, one just need to extract all the C-alpha from the `ATOM` record, then look at 4th column right?
```bash
grep ^ATOM pdb_file | grep CA | awk '{print $4}'
```
Then you can use the three-letter name as a dictionary lookup (the bash name is associative array -- fancy!) to produce one-letter name, so voilà we have a FASTA sequence?

```bash
declare -A aa_dict=(
[ALA]=A
[ARG]=R
[ASN]=N
[ASP]=D
[CYS]=C
[GLU]=E
[GLN]=Q
[GLY]=G
[HIS]=H
[ILE]=I
[LEU]=L
[LYS]=K
[MET]=M
[PHE]=F
[PRO]=P
[SER]=S
[THR]=T
[TRP]=W
[TYR]=Y
[VAL]=V
)
for resid in $(grep ^ATOM $1 | grep CA | awk '{print $4}'); do
    printf "%s" "${aa_dict[$resid]}"
done 
```

Indeed this was the first version of my script. And it works for the PDB file I was working with. But lo and behold it broke when I used it for a colleague's PDB file. The problem is some PDB files have alternative conformation and the residue names become `AMET`, `BMET` and the like, so the dictionary lookup broke. This is easily fixed by replacing `awk` column extraction with `cut` character extraction instead. (PDB format is an ancient format and it is strict with column character. This works in our favour)

```bash
grep ^ATOM pdb_file | grep CA | cut -c 18-20
```

This incident also got me revising my script to be more defensive. I also print out the three-letter to one-letter conversion with the residue numbers for the user to check. This way it is easy to see if there is any missing residue numbers or other anomalies. And I also add a few other cosmetic touches:
```bash
#extract_sequence_from_pdb_file.sh
declare -A aa_dict=(
[ALA]=A
[ARG]=R
[ASN]=N
[ASP]=D
[CYS]=C
[GLU]=E
[GLN]=Q
[GLY]=G
[HIS]=H
[ILE]=I
[LEU]=L
[LYS]=K
[MET]=M
[PHE]=F
[PRO]=P
[SER]=S
[THR]=T
[TRP]=W
[TYR]=Y
[VAL]=V
)

for resid_chain_resnum in $(grep ^ATOM $1 | grep CA | cut -c 18-26 | sed 's/ /_/g'); do
    printf "%s>%s\n" "$resid_chain_resnum" "${aa_dict[${resid_chain_resnum:0:3}]}"
done | sed 's/_/ /g' | pr --output-tabs=' 1' -5 -t
echo

echo ">$1"
for resid in $(grep ^ATOM $1 | grep CA | cut -c 18-20); do
    printf "%s" "${aa_dict[$resid]}"
done | fold
echo 
```

The output looks like this:
```
$ extract_sequence_from_pdb_file.sh 4WKQ.pdb
...
LYS A 757>K   CYS A 818>C   PRO A 877>P   PRO A 936>P   LEU A1017>L
GLU A 758>E   VAL A 819>V   ILE A 878>I   PRO A 937>P   ILE A1018>I
ILE A 759>I   GLN A 820>Q   LYS A 879>K   ILE A 938>I   PRO A1019>P
LEU A 760>L   ILE A 821>I   TRP A 880>W   CYS A 939>C   GLN A1020>Q
ASP A 761>D

>4WKQ.pdb
AMGEAPNQALLRILKETEFKKIKVLGSGTVYKGLWIPEGEKVKIPVAIKESPKANKEILDEAYVMASVDNPHVCRLLGIC
LTSTVQLITQLMPFGLLDYVREHKDNIGSQYLLNWCVQIAKGMNYLEDRRLVHRDLAARNVLVKTPQHVKITDFGLAKLL
GAEEKEYHAEGGKVPIKWMALESILHRIYTHQSDVWSYGVTVWELMTFGSKPYDGIPASEISSILEKGERLPQPPICTID
VYMIMVKCWMIDADSRPKFRELIIEFSKMARDPQRYLVIQGDMDDVVDADEYLIPQ
```
