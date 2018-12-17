---
title:  "Good practice for bash scripting"
date: 2018-12-18
permalink: /posts/2018/12/bash-good-practice/
categories: 
  - short
tags:
  - bash
  - coding
---

I will illustrate some good practices of writing bash scripts by showing you how I write and refactor my bash script `dock.sh` that does some preparation for docking and then launch docking.

1. Outline with pseudocode  
   What is it that you want the script to do? Our goal is to translate it to what the machine can understand. Here I have divided my script into 3 chunks, which are the sequence that I want my script to execute:
   ```bash
   main(){
   process_protein
   make_dock_input
   run_dock
   }
   
   process_protein(){
   loop over unprocessed proteins
   }
   
   make_dock_input(){
   need ligand path
   need protein paths --> loop
   }
   
   run_dock(){
   call docking programme
   }
   
   main
   ```
    Here I have defined a function `main` which overarches the 3 other functions. The reason for this structure is _abstraction_, such that we can think of the script at higher level -- that there are 3 things that the script does -- without needing to worry about the nitty-gritty inner workings of those 3 things. If need be, we can always zoom in and go to the lower level. It also makes it easy to comment out a whole functional block by just twiddling with `main`, like so:
    ```bash
    main(){
    process_protein
    make_dock_input
    #run_dock
    }
    ```
    Good-practice-wise, the function definition should be indented. Python would strictly enforce this, but not bash. I find my lack of indentation does not affect readability (and my script will have no nested functions where proper indentation becomes more important), so in this case I prefer no indentation.  
   
    ```bash
    # with proper indentation
    main(){
        process_protein
        make_dock_input
        run_dock
    }
    ```

2. If the outline looks alright, replace the pseudocode with the real code.  
   I am tackling the easiest first, `run_dock` (let's pretend the docking executable is `docking_programme`):

   ```bash
    run_dock(){
    docking_programme dock.inp
    }
    ```
    Then `process_protein` (assume `pdb_to_dck_converter` is a programme that does cleanups and preps for docking and outputs dock-ready file `.dck`):
    ```bash
    process_protein(){
    for pdb in *.pdb; do
        echo $i
        # basename replaces extension
        pdb_to_dck_converter -in $pdb -out $(basename -s .pdb $pdb).dck
    done
    }
    ```
    Constructing `for` loop is easy. We iterate for all `.pdb` files in the current directory (make sure the only `.pdb` files in the directory are the unprocessed ones). Note the deliberate indentation. `echo` is always good for future debugging. `basename` strips `.pdb` extension, to which we add `.dck` extension. If you think a line is not self-explanatory, write a comment.
    
    Now here is a trickier one: `make_dock_input`. Starting with a barebone template, I need to supply ligand and protein paths. The template looks like this:
    ```bash
    # template.dock.inp
    protein_paths	
    ligand_path	
    SOME_OTHER_PARAMETERS
    ```
    While I need the final input to look like this:
    ```bash
    # dock.inp
    PROTEIN /path/protein1.dck
    PROTEIN /path/protein2.dck
    PROTEIN /path/protein3.dck
    ...
    LIGAND /path/ligand.lig	
    SOME_OTHER_PARAMETERS
    ```
    At first, I thought a simple `sed` substitution will do:
    ```bash
    # doesn't work
    ligand_path="/path/ligand.lig"
    sed "s/ligand_path/$ligand_path" template.dock.inp > dock.inp
    ```
    But no, there are some illegal characters in `$ligand_path`. There are some clever cleanups or escaping that can be done, but in the end I stick to simple insertion and deletion. Cleanups will result in unnecessarily more lines and using escape characters will make the code much less readable.
    ```bash
    # insert at the line before 'placeholder' ligand_path
    ligand_path="/path/ligand.lig"
    echo $ligand_path
    sed "ligand_path/i $ligand_path" template.dock.inp > dock.inp
    # delete 'placeholder' ligand_path
    sed -i '/ligand_path/d' dock.inp
    ```
    How about protein paths? The logic is the same as ligand path; just wrap it in a loop:
    ```bash
    for pdb in *.pdb; do
        # insert at the line before 'placeholder' protein_path
        dck=$(basename -s .pdb $pdb).dck
        protein_path="PROTEIN /path/$dck"
        echo $protein_path
        sed -i "/protein_paths/i $protein_path" dock.inp
    done
    # delete 'placeholder' protein_paths
    sed -i '/protein_paths/d' dock.inp
    ```
    Combine them:
    ```bash
    make_dock_input(){
    # insert at the line before 'placeholder' ligand_path
    ligand_path="/path/ligand.lig"
    echo $ligand_path
    sed "ligand_path/i $ligand_path" template.dock.inp > dock.inp
    # delete 'placeholder' ligand_path
    sed -i '/ligand_path/d' dock.inp
    
    for pdb in *.pdb; do
        # insert at the line before 'placeholder' protein_path
        dck=$(basename -s .pdb $pdb).dck
        protein_path="PROTEIN /path/$dck"
        echo $protein_path
        sed -i "/protein_path/i $protein_path" dock.inp
    done
    # delete 'placeholder' protein_path
    sed -i '/protein_paths/d' dock.inp
    }
    ```
3. Debug and refactor  
   Test the script. Does it do what you want to do? If it does, great! But your work is not done yet. You should put some effort in _refactoring_, which does not change the script output, but beautify the source code, so to speak. What you want is source code that is readable and maintainable.
   For a start, you may want to combine some lines:
   ```bash
   sed -i '/ligand_path/d' dock.inp
   sed -i '/protein_paths/d' dock.inp
   # one line
   sed -i '/ligand_path/d; /protein_paths/d' dock.inp
   ```
   Still very much readable, isn't it?
   
   Now take a look at our function and variable names. Are there unclear or meaningless ones like `$x` or `$i`? Though there is nothing wrong with `$i` for a throwaway variable in a `for` loop for instance, replacing it with a more explicit one is better. Compare:
   ```bash
   for i in *pdb; do
       #do things with $i
       ...
   done
   ```
   to:
   ```bash
   for pdb in *pdb; do
       #do things with $pdb
       ...
   done
   ```
   Or even:
   ```bash
   for pdb_file in *pdb; do
       #do things with $pdb_file
       ...
   done
   ```
   Another example -- you might decide that this
   ```bash
   sed -i "/protein_path/i $protein_path" dock.inp
   ```
   is a little bit confusing since `protein_path` is repeated. This might be better:
   ```bash
   # more explicit
   sed -i "/protein_path_placeholder/i $protein_path" dock.inp
   ```
   Lastly, add more comments, which is valuable documentation, especially when future you digs out this script again in far future! Take a look again at how I wrote the comments for insertion and deletion steps in `make_dock_input`. On the other hand, do not overcomment.

   I read somewhere that typically for a given code one writes 10 times as much code! That should give you some idea of how much editing is done.
   
### BONUS: how to specify a dynamic list 
This is more of a tip than good practice. I often find myself needing to do things to a list of proteins, but I want to exclude and include some of them with flexibility. In the example above, `for pdb in *pdb` will iterate over all `.pdb` files in the directory, so the list is fixed.

The solution that I like is bash array:
```bash
# quotation marks are optional
pdbs=(
protein_a.pdb
protein_b.pdb
#protein_c.pdb # easy excluding by commenting
protein_d.pdb
)
```
The only drawback is that calling the array is not something I can do off the top of my head:
```bash
for pdb in "${pdbs[@]}"; do
    ...
done
```
If you are calling the list from several different scripts, put the array into a master list file `list.sh`. In the scripts you just need to include `source /path/list.sh`. Now you just need to edit the master list once.
