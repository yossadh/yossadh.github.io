---
title:  "Conditional execution in bash"
date: 2019-05-24
permalink: /posts/2019/05/bash-conditional-execution/
categories: 
  - short
tags:
  - bash
---
Situation: You want to execute a script as soon as a particular file is created.
  
This is an easy one-liner:
```bash
# waiter.sh
# Usage: ./waiter.sh script_to_be_run file_to_anticipate [time_delay, def. 10 mins] 
while [ ! -f $2 ]; do sleep ${3-10m}; done; bash $1
```
The while loop keeps checking the existence of the specified file: while the file **does** not exist the loop will run `sleep` on on and on. When the file exist, loop will be exited and the specified script will be run. Could it get any simpler? It seems like a waste just to continually check, but I don't see a better option for now. If you are *wu liao* run `bash -x waiter.sh` to see how many `sleep`s your `waiter.sh` has to go through to finally run your desired script.
  
One learning point -- I was using this kind of thing before to make a positional argument optional:
```bash
[ "$3" = "" ] && time_delay="15m" || time_delay=$3
```
This is a "ternary" bash construction, which you can construe as a shorthand conditional: `A && B || C` means "if A is true; execute B; else execute C".
  
Turns out the easier way is using shell parameter expansion magic: 
```bash
time_delay=${3-15m}  # if $3 is unset, set $3 to 15m
time_delay=${3:-15m} # if $3 is unset or null, set $3 to 15m
```
