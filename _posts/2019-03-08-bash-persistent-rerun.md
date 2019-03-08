---
title:  "How to persistently run your script with Bash"
date: 2019-03-08
permalink: /posts/2019/03/bash-persistent-rerun/
categories: 
  - short
tags:
  - bash
---
This is a simple script that saves a lot of headaches for me. The situation: I'm running a proprietary software and it seems that there is a problem with the way our license server is setup, because from time to time the process will stop due to "not enough license" even though we have enough. Then we have to rerun, which is ok because there are checkpoint files, but it is a pain to constantly check whether my run has crashed or not.

The solution is simple, and I'm bashing (lol) myself for not writing this ten-liner much earlier. The logic is basically a conditional loop with time delays. We keep trying to rerun every `$time_delay` and exit the loop if certain condition is satisfied. In my case, the condition is the string `All runs succeeded` appears in the log file.

This is basically first (and maybe last) time I ever used the `until` loop. It is just the opposite of `while`, so it doesn't get used a lot since you can just negate the condition and use `while` instead. So this is the main logic:

```bash
until grep "All runs succeeded" $log; do
    sleep $time_delay
    $RUN_PROGRAM $inp 
done
```

Then the rest is just cosmetic. I add default `$time_delay` of 15 mins if none is specified; I avoid time delay for the first loop iteration; I echo some (encouraging) messages to `stdout`; and finally add a pop-out `zenity` window once the whole shebang is done. 

```bash
# rerun.sh
# usage: ./rerun.sh [input_file] [log_file] [time_delay (opt.), default is 15mins]

RUN_PROGRAM=your_program
inp=$1
log=$2
[ "$3" = "" ] && time_delay="15m" || time_delay=$3

counter=0
until grep "All runs succeeded" $log; do
    [ $counter -gt 0 ] && sleep $time_delay
    echo "Retry #$counter | time delay is $time_delay"
    $RUN_PROGRAM $inp 
    ((counter+=1))
done

zenity --info --text "$0 done"
```
