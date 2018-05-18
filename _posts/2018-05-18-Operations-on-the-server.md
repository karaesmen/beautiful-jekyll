---
layout: post
title: Operations on the server
subtitle: Life saver one liners that I manage to forget every time
image: /img/kirpi_sq.JPG
tags: [unix, shell]
---



<!-- # Directories -->
<!-- ## First Phase, Survival Result Files -->
<!-- Results files contain SNP information and statistics such as p-values, coefs, CIs ans hazard ratios from the applied model. Each result file is located in a folder that has the same name as the file. Result file directories of imputed data is given below. -->
<!-- - **The last most common directory for all these files is:** -->
<!-- **`/projects/rpci/lsuchest/lsuchest/Rserve/BMT/ImputeData/var/db/gwas/imputed_data/`** -->
<!-- **From this directory below other files can be found at:** -->
<!-- - **Results of cohorts 1 & 2 for independent donor & recipient genomes:**       -->
<!-- **`./BMT093013_forImpute/analyses/`**  -->
<!-- - **Results of cohort 1 & 2 for shared genome:**    -->
<!-- **`./SHARED/analyses/`**      -->
<!-- - **Results of the meta analysis for independent recipient & donor genomes:** -->
<!-- **`./BMT093013_forImpute/analyses/METAL.results/`** -->
<!-- - **Results of the meta analysis for shared genome:**    -->
<!-- **`./SHARED/analyses/METAL.RESULTS.SHARED`** -->
<!-- - **The info file for the entire imputation:**   -->
<!-- **`./BMT093013_forImpute/Impute2_summary`** -->



`awk`
=====

Print the number of fields in a file
------------------------------------

`-d` delimiter argument here it is space (`" "`), and `n` is should be replaced with a number of max number of columns to be printed.

``` bash
cut -d ' ' -f 1-n file | head
```

Subset analysis results for a set of rsIDs
------------------------------------------

Following code subsets the result files for a set of rsIDs.
`File2` is the file only contatining the list of SNPs to be subsetted.
`File1` is the main file (e.g. result files) to be subsetted.
Code looks for mathcing elements in column 2 of `File1`

``` bash
awk 'NR==FNR{pats[$0]; next} $2 in pats' File2 File1
```

Exclude certain rows on the condition upon certain colums
---------------------------------------------------------

Following code excludes any lines that satisfy the conditional `$3=="chr6" && 30000<$4 && $4<50000` and outputs all fields for all other rows. The conditional `$3=="chr6" && 30000<$4 && $4<50000` means: **"anything in column 3 that is `chr6` that ranges from `30000` to `50000`"**

``` bash
awk 'NR==1 || !($3=="chr6" && 30000<$4 && $4<50000)'  file
```

To return only the selected fields from the operation above simply define the columns you want to be outputted with `{print $}`.

``` bash
awk 'NR==1 || !($3=="chr6" && 30000<$4 && $4<50000) {print $1, $2}'  file
```

Print first n columns
---------------------

Code below subsets for the first 10 columns. `-d` also defines the delimiter type of the file, which is space in this example.

``` bash
cut -d " " -f 1-10 file
```

ifelse examples
---------------

See some nice examples [here](http://www.thegeekstuff.com/2010/02/awk-conditional-statements)

Other operations
================


<!-- 
for loops
---------

``` bash
#!/bin/sh


for chrom in `seq 1 22` 
do

ls -l BMT093013_forImpute.chr${chrom}-*.impute2 | awk '{print $NF}' > /projects/rpci/lsuchest/ezgi/GenexDrug/impute2.chunk.lists/chr${chrom}.impute2

ls -l BMT093013_forImpute.chr${chrom}-*.impute2 | awk '{print $NF"_samples"}' > /projects/rpci/lsuchest/ezgi/GenexDrug/impute2.chunk.lists/chr${chrom}.impute2_samples



paste /projects/rpci/lsuchest/ezgi/GenexDrug/impute2.chunk.lists/chr${chrom}.impute2 /projects/rpci/lsuchest/ezgi/GenexDrug/impute2.chunk.lists/chr${chrom}.impute2_samples > /projects/rpci/lsuchest/ezgi/GenexDrug/impute2.chunk.lists/chr${chrom}.ImputeFiles
rm /projects/rpci/lsuchest/ezgi/GenexDrug/impute2.chunk.lists/chr${chrom}.impute2
rm /projects/rpci/lsuchest/ezgi/GenexDrug/impute2.chunk.lists/chr${chrom}.impute2_samples
 

done
```

 -->

Nested loop example
-------------------

Following code was written to generate example data sets for the gwasurvivr package. It subsets the file `chr1_good_snps.impute` for rows (`snps`) and for samples (`n`). Also this shows how to do calculations on unix and assign it to a new variable. In this case `let` function is used to define `col`, which is the fields (columns) of the impute file. 5 is for the first 5 columns of the file defining the snps, and `*3` is for each genotype probability of a given sample.
*Note:* Be careful with the format of the file name. Do not use `_` after the for loop variables, it doesn't print the expected file name. (quite likely `_` has a syntax meaning...)

``` bash
for snps in 100 1000 10000 100000 150000
do
    for n in 100 1000 5000
    do
        let col=$n*3+5 
      head -$snps chr1_good_snps.impute | cut -d " " -f 1-$col > n$n.snp$snps.chr1.impute
      echo "snp $snp n $n "
    done
done

echo "All Done!"
exit
```

Change the file permissions to executable
-----------------------------------------

``` bash
chmod +x filename
```
