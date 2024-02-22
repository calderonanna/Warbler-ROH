# Calling ROH
Calling ROH is done using GARLIC, which calculates the probability that a variant is within autozygosity. It assigns each variant an LOD (log of odds) score and then sums up all LOD scores for a given window.  To use the program, it requires 3 input files that need to be created from vcf files (.tped, .tfam and .tgls) and a file with the centromere positions. First we will take some steps to create the necessary files before running GARLIC
https://github.com/szpiech/garlic


## 1. Creating tgls files 
We need to extract the genotype quality for each site and each genotype. The tgls files will be generated from the zipped vcf files using a python script that Zach wrote. The tgls file extracts the genotype probability and gives a per genotype error rate. 

```bash
nano create_tgls.bash
------------------------------------------------------NANO------------------------------------------------
#!/bin/bash
#PBS -l feature=rhel7
#PBS -l nodes=1:ppn=1
#PBS -l walltime=15:00:00
#PBS -l mem=500gb
#PBS -A wff3_a_g_hc_default

cd ~/SzpiechLab/abc6435/WarblerROH/vcf
#citrina 
./create_tgls_from_vcf.py citrina_filtered.vcf.gz > ~/SzpiechLab/abc6435/WarblerROH/tgls_tped_tfam/citrina.tgls

#ruticilla
./create_tgls_from_vcf.py ruticilla_filtered.vcf.gz > ~/SzpiechLab/abc6435/WarblerROH/tgls_tped_tfam/ruticilla.tgls

#kirtlandii
./create_tgls_from_vcf.py kirtlandii_filtered.vcf.gz > ~/SzpiechLab/abc6435/WarblerROH/tgls_tped_tfam/kirtlandii.tgls

--------------------------------------------END OF SCRIPT------------------------------------------------
qsub create_tgls.bash
```


