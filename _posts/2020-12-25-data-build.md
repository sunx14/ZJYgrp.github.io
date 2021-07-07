---
layout: post
title: "Extract PDB data from website by API"
date: 2020-12-25
categories: CATEGORY-1 CATEGORY-2
comments: true
---

### database_construction
This git repo is used to build the workflow for merging two existing databases, protein structure databank and kinetics databank. 


**Download html file to view in a browser to have a full better view of jupyter notebook.**

**If you don't wanna downlaod, you can click `ipynb` file in `code` tab to view part of them within GitHub directly.**

# database_construction
This git repo is used to build the workflow for merging two existing databases, protein structure databank and kinetics databank.


## The second week

Problems: how to pull pdb data from URL and extract useful data, like `atom` and `SEQRES`.
Two parts are focus in this week.

1. The first part is to research on the documentation of package `pypdb` which is "A Python API for the RCSB Protein Data Bank". So I looked all the function, find `get_pdb_file()` function which can download specific pdb entry in specific format.
2. The next part is that I didn't find any related function in package `pypdb` which is able to solve our problems. So I wrote my own function using basic way, like list and loop to solve this probelm and get a list of `atom` and `SEQRES` which is what we want.
    
## The third week

Problems: I want to change the list I got into a dataframe which is doable and add columns to include id then merge multiple pdb. file.

This week from 2020.10.11 to 2020.10.12, I worked on how to use `pandas` package to manipulate data, like mergeing two data frames.

1. So I first used `split()` function to convert all the lists I got from last week into a `pandas` dataframs. I used `split()` to split a long string by white space. Then use `pd.DataFrame()` function to convert it. And use `concat()` to merge multiple dataframes.
    
2. I look around `MdTraj` package to do some data manipulation, like loading pdb file into memroy from URL, getting topology strcture to convert atoms into `pandas` dataframe,  using `stom_slice()` to slice atoms by indexs and so on.

## The fourth week

Problems: We want to include all atoms and hetatms into one data frame.

1. I wrote two functions to return all info. 
    * `get_all_atom()` would return all atom, inclduing hetatm, ter, namely the whole part will be returned.                   
    * `get_atom_and_hetatm()` only return atom and hetatm.

2. I wrote a class of one pdb file which has all the methods which can return the desired attribution of one pdb file. And I tested all of them. They worked well.

## The fifth week

Problems: We want to create two tables for each pdb file, one is called general table, another is called atom_htmatm table and merge multiple of them into one longer table.

1. I wrote one function called `get_general_table()` which gets all the desired fields inclduing residue sequences.

2. Another function is called `get_atom_hetatm_table()` which only includes atom and htmatm, this table also joins with another atom mass table, the final table can show each atom mass.

At last, I fixed a bug from the previous week, our `get_missing_residue()` function can accommodate more missing residue types.

## The sixth - eighth week

1. I found a bug in `get_enzyme_type()` method, some PDB files have multiple words for enzyme type splitted by one white space. So I chose the Date as the separator to clip the enzyme type on the first line.

2. The second bug is in `get_general_table()` method, some PDB files don't have missing residues parts, so I added `if isinstance(Missing, str) or Missing is None:` extra condition to deal with this situation when it doesn't have missing residues.

3. I added one extra column `name` which is the original sign of atom side by the full atomic name column called `Name` in the data frame returned by `get_atom_hetatm_table()` method at the same time I changed the function from `join()` to `merge()` when I concatenate two pandas data frames.

4. Show the atomic table in the file which can be used to merge with atom and hetatm table.

5. **We found that the average time of process random 100 PDB files is 248.84s. So from the formula, we have in the email you sent me, I calculated we need around 5 days to extract all general table and atom and hetatm tabled of 170k PDB files.** 

6. I found that there were a couple of PDB files which are None, so I wrote some control flows to raise warnings when the PDB files are `None`. After searching on the RCSB PDB [https://www.rcsb.org/structure/4U20](https://www.rcsb.org/structure/4U20) website, we found that this PDB has a corresponding file, but because of the huge file size, we can't access the PDB format as other files. It only provides PDBx format which is beyond our current class capacity. 

7. I also found there are some PDB files which have a PDB format file but lack some desired fields. I wrote some control flows to handle the issue. When the field we want is a lack in the PDB file, we raise warnings to tell users that this field is missing in this file.

8. I found another bug in `get_name()` method when the name in the PDB file has multiple lines to be recorded. So I added additional codes to deal with this situation `if ";" in res:`. We can test this situation using `pdb("100d").get_name()` and we get the result `DNA/RNA (5'-R(*CP*)-D(*CP*GP*GP*CP*GP*CP*CP*GP*)-R(*G)-3')`.

9. *We want to write a check function or process, but I have my own opinion,* first, I found this is a very hard process, because there are a lot of PDB files and their formats in PDB file are not unified, there is no standard format in these PDB files. So a function or process which can check each entry extracted from these methods is still biased and not accurate. So I suggest we correct and fix bugs when we find them while we use these tables.

## The first winter break work

1. Corrected all warning messages. Specify what field is missing in warning messages of each `get_*()` method.

2. Fixed a bug in `get_chain_id()` method. Because one protein may have multiple entities including multiple chains in different lines in one pdb file. Now iterate all lines in pdb files to search all chains.

3. Fixed another bug. In the past, I used `len()` to get number of one sequence in the table extracted. But there are some `None` at the end of this table. so I decided to use numbers provided in the pdb file which is exact.

4. Extract `FASTA` Sequence of all chains in one pdb file using web grab technology. The method of class is `get_fasta()`. Add this information as a column in `general_table()`.

5. Extract Missing Residue of all chains inclduing yellow one and grey one. This took me some time to finish because I used new PDB Data GraphiQL query syntax connected to Data API and embedded this URL encoded query in python to grab information in websites. This method of class is `get_part_mod_resi()` for yellow box and `get_unmod_resi()` for grey box.

6. Extract Entity ID with its belonging chains. The method of class is `get_entity_id()`.

7. Extract Global Stoichiometry of each pdb file using Web Grab technology. The method of class is `get_glo_sto()`.

8. Extract Mutation field in each pdb website using Web Grab technology. The method of class is `get_mutation()`.