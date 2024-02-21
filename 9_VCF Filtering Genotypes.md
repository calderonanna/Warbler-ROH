# VCF Filtering Genotypes

## Filter 1: Supporting Reads

Filter 1: All genotypes required a minimum of 6 supporting reads in accordance with Robin et al. 2016, and a maximum of 34 supporting reads. Maximum supporting reads were based on the highest 95th percentile value of all samples. 

### Calculating DP thresholds per sample
The python script I will be using to calculate the DP thresholds is 

```bash
nano Calculate_DP.bash
------------------------------------------------------NANO------------------------------------------------
#!/bin/bash
#PBS -l feature=rhel7
#PBS -l nodes=1:ppn=1
#PBS -l walltime=24:00:00
#PBS -l mem=100gb
#PBS -A wff3_a_g_hc_default

cd /gpfs/group/zps5164/default/abc6435/WarblerROH/vcf

python extractDP.py citrina_ExHet.vcf.gz > /storage/home/abc6435/work/citrina_DP.txt
python extractDP.py kirtlandii_ExHet.vcf.gz > /storage/home/abc6435/work/kirtlandii_DP.txt
python extractDP.py ruticilla_ExHet.vcf.gz > /storage/home/abc6435/work/ruticilla_DP.txt

------------------------------------------------------END------------------------------------------------

rsync abc6435@submit.aci.ics.psu.edu:/storage/home/abc6435/work/citrina_DP.txt /Users/abc6435/Desktop/WROH/data

rsync abc6435@submit.aci.ics.psu.edu:/storage/home/abc6435/work/kirtlandii_DP.txt /Users/abc6435/Desktop/WROH/data

rsync abc6435@submit.aci.ics.psu.edu:/storage/home/abc6435/work/ruticilla_DP.txt /Users/abc6435/Desktop/WROH/data
```
```r
#Calculate DP Thresholds in R for the three species
cit_dp <- as.data.frame(read.table("/Users/abc6435/Desktop/citrina_DP.txt"))
cit_dp <- rename(cit_dp, POS=POS, c262=V5, c2871=V6, c104049=V7, 
       c107057=V8, cTE22T01=V9, cTE30T02=V10, cSE25T02=V11)
quantile(cit_dp$c262, probs = c(0.05, 0.95))
quantile(cit_dp$c2871, probs = c(0.05, 0.95))
quantile(cit_dp$c104049, probs = c(0.05, 0.95))
quantile(cit_dp$c107057 , probs = c(0.05, 0.95))
quantile(cit_dp$cTE22T01, probs = c(0.05, 0.95))
quantile(cit_dp$cTE30T02, probs = c(0.05, 0.95))
quantile(cit_dp$cSE25T02, probs = c(0.05, 0.95))

#MAX DP: 34
```
### Filtering individual genotypes based on DP thresholds
```bash 
#test(pass)
bcftools filter test_citrina_ExHet.vcf -e 'FMT/DP<6 | FMT/DP>34' -S .  -Oz -o test_citrina_gtDP.vcf.gz

#Execute Filter
nano genotype_DP.bash
------------------------------------------------------NANO------------------------------------------------
#!/bin/bash
#PBS -l feature=rhel7
#PBS -l nodes=1:ppn=1
#PBS -l walltime=15:00:00
#PBS -l mem=200gb
#PBS -A wff3_a_g_hc_default

#citrina
bcftools filter ~/SzpiechLab/abc6435/WarblerROH/vcf/citrina_ExHet.vcf.gz -e 'FMT/DP<6 | FMT/DP>34' -S .  -Oz -o ~/SzpiechLab/abc6435/WarblerROH/vcf/citrina_gtDP.vcf.gz

#ruticilla
bcftools filter ~/SzpiechLab/abc6435/WarblerROH/vcf/ruticilla_ExHet.vcf.gz -e 'FMT/DP<6 | FMT/DP>34' -S .  -Oz -o ~/SzpiechLab/abc6435/WarblerROH/vcf/ruticilla_gtDP.vcf.gz

#kirtlandii
bcftools filter ~/SzpiechLab/abc6435/WarblerROH/vcf/kirtlandii_ExHet.vcf.gz -e 'FMT/DP<6 | FMT/DP>34' -S .  -Oz -o ~/SzpiechLab/abc6435/WarblerROH/vcf/kirtlandii_gtDP.vcf.gz
------------------------------------------------------END OF SCRIPT-------------------------------------
qsub genotype_DP.bash
```

## Filter 2: Filter GQ
Genotypes with quality scores less than 20 were set to missing. 
```bash
#test(pass)
bcftools filter test_citrina_gtDP.vcf.gz -e 'FMT/GQ<20' -S . -Oz -o test_citrina_gtGQ.vcf.gz

#Execute Filter
nano genotypeGQ_.bash
------------------------------------------------------NANO------------------------------------------------
#!/bin/bash
#PBS -l feature=rhel7
#PBS -l nodes=1:ppn=1
#PBS -l walltime=5:00:00
#PBS -l mem=500gb
#PBS -A wff3_a_g_hc_default

#citrina
bcftools filter ~/SzpiechLab/abc6435/WarblerROH/vcf/citrina_gtDP.vcf.gz -e 'FMT/GQ<20' -S . -Oz -o ~/SzpiechLab/abc6435/WarblerROH/vcf/citrina_gtGQ.vcf.gz

#kirtlandii
bcftools filter ~/SzpiechLab/abc6435/WarblerROH/vcf/kirtlandii_gtDP.vcf.gz -e 'FMT/GQ<20' -S . -Oz -o ~/SzpiechLab/abc6435/WarblerROH/vcf/kirtlandii_gtGQ.vcf.gz

#ruticilla
bcftools filter ~/SzpiechLab/abc6435/WarblerROH/vcf/ruticilla_gtDP.vcf.gz -e 'FMT/GQ<20' -S . -Oz -o ~/SzpiechLab/abc6435/WarblerROH/vcf/ruticilla_gtGQ.vcf.gz
------------------------------------------------------END OF SCRIPT-------------------------------------
qsub genotype_GQ.bash
```
## Filter 3: Allele Balance (Skipped)
Finally, heterozygous genotypes required an allele balance (ratio of reads containing the reference allele out of total reads in individuals called as heterozygous) between 0.23 and 0.76. Note: This step was skipped because I do not have AB in my vcf header. Will go back to the GATK steps and ask for these statistics. 
```bash

 bcftools view -g het -i  "AB>0.25 && AB < 0.75" test_citrina_gtGQ.vcf.gz

``` 

