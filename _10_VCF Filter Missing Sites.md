
# VCF Filtering Sites by Missing GT
Filtering Sites by Species based on missing GT thresholds. Include any sites with less than 3 missing GT

```bash 
nano filter_missingGT.bash
------------------------------------------------------NANO------------------------------------------------
#!/bin/bash
#PBS -l feature=rhel7
#PBS -l nodes=1:ppn=1
#PBS -l walltime=15:00:00
#PBS -l mem=300gb
#PBS -A wff3_a_g_hc_default

cd ~/SzpiechLab/abc6435/WarblerROH/vcf

#citrina
bcftools view -i 'F_MISSING<.42' citrina_gtGQ.vcf.gz -Oz -o citrina_FMISSING.vcf.gz
#kirtlandii
bcftools view -i 'F_MISSING<.42' kirtlandii_gtGQ.vcf.gz -Oz -o kirtlandii_FMISSING.vcf.gz
#ruticilla
bcftools view -i 'F_MISSING<.42' ruticilla_gtGQ.vcf.gz -Oz -o ruticilla_FMISSING.vcf.gz

--------------------------------------------END OF SCRIPT------------------------------------------------
qsub filter_missingGT.bash
```

