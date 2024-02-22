
# 2. Creating and modifying tped and tfam files
Garlic uses tped and tfam files as input. The tped file is basically a summary of the genotypes of each sample at each position. The tfam file is  the first six columns of a standard PED file, where plink took the standard PED/MAP file format, and swapped all the genotype information between files, after rotating it 90 degrees🤷🏻‍♀️. This process will also create a ` .log` and `.nosex` file: 

`plink --vcf input.vcf.gz --recode transpose --double-id --chr-set nb_of_chromo --allow-extra-chr --out path/suffix`

-   `--vcf`  path to vcf file
-   `-chr-set`  number of chromosomes
-   `--out`  path and suffix for the output files
-   `--recode transpose`  to generate tped and tfam files
-   `--double-id`  because ind are named with _ names
-  `--allow-extra-chr` forces plink to accept non-numerical chromosomes names like "chr1a", for example

To run Garlic, the first column of the tfam file must consist of the individuals population. We will need to modify the tfam file using awk to add the right population names using this line of code: 

`awk '$1="pop_name"' path/input.tfam > path/output.tfam`

```bash
nano create_tped_tfam.bash
------------------------------------------------------NANO------------------------------------------------
#!/bin/bash
#PBS -l feature=rhel7
#PBS -l nodes=1:ppn=1
#PBS -l walltime=15:00:00
#PBS -l mem=600gb
#PBS -A wff3_a_g_hc_default

cd ~/SzpiechLab/abc6435/WarblerROH/vcf
#citrina TPED and TFAM
plink --vcf citrina_filtered.vcf.gz --recode transpose --double-id --chr-set 30 --allow-extra-chr --out ~/SzpiechLab/abc6435/WarblerROH/tgls_tped_tfam/citrina

awk '$1="citrina"' citrina.tfam > citrina_mod.tfam

#ruticilla TPED and TFAM
plink --vcf ruticilla_filtered.vcf.gz --recode transpose --double-id --chr-set 30 --allow-extra-chr --out ~/SzpiechLab/abc6435/WarblerROH/tgls_tped_tfam/ruticilla

awk '$1="ruticilla"' ruticilla.tfam > ruticilla_mod.tfam

#kirtlandii TPED and TFAM
plink --vcf kirtlandii_filtered.vcf.gz --recode transpose --double-id --chr-set 30 --allow-extra-chr --out ~/SzpiechLab/abc6435/WarblerROH/tgls_tped_tfam/kirtlandii

awk '$1="kirtlandii"' kirtlandii.tfam > kirtlandii_mod.tfam
-------------------------------------------------END OF FILE----------------------------------------------
qsub create_tped_tfam.bash
```
