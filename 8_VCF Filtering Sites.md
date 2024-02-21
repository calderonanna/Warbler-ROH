# VCF Filtering Sites
https://www.reneshbedre.com/blog/vcf-fields.html
https://samtools.github.io/bcftools/howtos/variant-calling.html
https://samtools.github.io/bcftools/howtos/filtering.html


## File Prep 
```bash
nano bgzip_seto.bash
------------------------------------------------------NANO------------------------------------------------
#!/bin/bash
#PBS -l feature=rhel7
#PBS -l nodes=1:ppn=1
#PBS -l walltime=100:00:00
#PBS -l mem=500gb
#PBS -A wff3_a_g_hc_default
bgzip -c ~/SzpiechLab/abc6435/WarblerROH/Setophaga.vcf > ~/SzpiechLab/abc6435/WarblerROH/Setophaga.vcf.gz
--------------------------------------------------END OF FILE---------------------------------------------
qsub bgzip_seto.bash

bcftools index Setophaga.vcf.gz
```

## Filter 1: Read Depth
Filter 1: Sites with **combined** read depth across individuals less than 255 or greater than 401 were filtered out (these values correspond to 5th and 95th percentiles for total depth of our samples, respectively)

```bash
nano filterDP.bash
------------------------------------------------------NANO------------------------------------------------
#!/bin/bash
#PBS -l feature=rhel7
#PBS -l nodes=1:ppn=1
#PBS -l walltime=15:00:00
#PBS -l mem=500gb
#PBS -A wff3_a_g_hc_default
bcftools view -e 'SUM(FMT/DP)<255 | SUM(FMT/DP)>401' ~/SzpiechLab/abc6435/WarblerROH/vcf/Setophaga.vcf.gz -Oz -o ~/SzpiechLab/abc6435/WarblerROH/vcf/Setophaga_DP.vcf.gz
--------------------------------------------------END OF FILE---------------------------------------------
qsub filterDP.bash
```

## Filter2: Site Quality 
```bash
nano filterQUAL.bash
------------------------------------------------------NANO------------------------------------------------
#!/bin/bash
#PBS -l feature=rhel7
#PBS -l nodes=1:ppn=1
#PBS -l walltime=15:00:00
#PBS -l mem=500gb
#PBS -A wff3_a_g_hc_default
bcftools view -i '%QUAL>=50' ~/SzpiechLab/abc6435/WarblerROH/vcf/Setophaga_DP.vcf.gz -Oz -o ~/SzpiechLab/abc6435/WarblerROH/vcf/Setophaga_QUAL.vcf.gz
--------------------------------------------------END OF FILE---------------------------------------------
qsub filterQUAL.bash
```

## Filter3: ADF/ADR (Skipped)
Sites required at least 2 of the same alternate allele observations on each of the forward and reverse strands.  Note: My vcf file doesn't have this information in the header. 

## Filter4: Biallelic Sites
Multi-nucleotide polymorphisms, indels, complex variants, and sites with more than one alternate allele were excluded, as were sites with more than two missing genotypes. (See https://www.biostars.org/p/141156/)

```bash
nano filterbiallelic.bash
------------------------------------------------------NANO------------------------------------------------
#!/bin/bash
#PBS -l feature=rhel7
#PBS -l nodes=1:ppn=1
#PBS -l walltime=15:00:00
#PBS -l mem=500gb
#PBS -A wff3_a_g_hc_default
bcftools view -m2 -M2 -v snps ~/SzpiechLab/abc6435/WarblerROH/vcf/Setophaga_QUAL.vcf.gz -Oz -o ~/SzpiechLab/abc6435/WarblerROH/vcf/Setophaga_biallelic.vcf.gz 
--------------------------------------------------END OF FILE---------------------------------------------
qsub filterbiallelic.bash

```
## Sanity Check
```bash 
qsub -A zps5164_a_g_hc_default -l feature=rhel7 -l walltime=10:00:00 -l nodes=1:ppn=1 -l mem=100gb -I

#Produce stats after every filter
bcftools stats Setophaga.vcf.gz > Setophaga_stats.txt
bcftools stats Setophaga_DP.vcf.gz > Setophaga_DP_stats.txt
bcftools stats Setophaga_QUAL.vcf.gz > Setophaga_QUAL_stats.txt
bcftools stats Setophaga_biallelic.vcf.gz > Setophaga_biallelic_stats.txt

```
  
## Subset species 
```bash
nano subsetsp.bash
------------------------------------------------------NANO------------------------------------------------
#!/bin/bash
#PBS -l feature=rhel7
#PBS -l nodes=1:ppn=1
#PBS -l walltime=24:00:00
#PBS -l mem=500gb
#PBS -A wff3_a_g_hc_default

#Subset by citrina samples
bcftools view -s 262,2871,TE22T01,TE30T02,SE25T02 ~/SzpiechLab/abc6435/WarblerROH/vcf/Setophaga/Setophaga_biallelic.vcf.gz -Oz -o ~/SzpiechLab/abc6435/WarblerROH/vcf/citrina.vcf.gz

#Subset by kirtlandii samples
bcftools view -s 183195332,183194861,183195321,183195304,183194841,183195326,183195312 ~/SzpiechLab/abc6435/WarblerROH/vcf/Setophaga/Setophaga_biallelic.vcf.gz -Oz -o ~/SzpiechLab/abc6435/WarblerROH/vcf/kirtlandii.vcf.gz

#Subset by ruticilla samples
bcftools view -s 163,1049,1940,4056,284029323,TF19T04,TF03T03 ~/SzpiechLab/abc6435/WarblerROH/vcf/Setophaga/Setophaga_biallelic.vcf.gz -Oz -o ~/SzpiechLab/abc6435/WarblerROH/vcf/ruticilla.vcf.gz
--------------------------------------------------END OF FILE---------------------------------------------
qsub subsetsp.bash
```

## Filter5: Excess Heterozygosity
For ruticilla and kirtlandii, each with a n=7, sites with excess heterozygosity were defined as sites with 6 or more heterozygote genotypes and were excluded. For citrina (n=5), excess heterosygosity was defined as sites with 4 or more heterozygote genotypes and were excluded. 

```bash
nano filterExHet_cit.bash
------------------------------------------------------NANO------------------------------------------------
#!/bin/bash
#PBS -l feature=rhel7
#PBS -l nodes=1:ppn=1
#PBS -l walltime=30:00:00
#PBS -l mem=200gb
#PBS -A wff3_a_g_hc_default

#citrina filter excess heterozygotes
bcftools view -e 'COUNT(GT="het")>=4' ~/SzpiechLab/abc6435/WarblerROH/vcf/citrina.vcf.gz -Oz -o ~/SzpiechLab/abc6435/WarblerROH/vcf/citrina_ExHet.vcf.gz
--------------------------------------------------END OF FILE---------------------------------------------
qsub filterExHet_cit.bash
```

```bash
nano filterExHet_kirt.bash
------------------------------------------------------NANO------------------------------------------------
#!/bin/bash
#PBS -l feature=rhel7
#PBS -l nodes=1:ppn=1
#PBS -l walltime=30:00:00
#PBS -l mem=200gb
#PBS -A wff3_a_g_hc_default

#kirtlandii filter excess heterozygotes
bcftools view -e 'COUNT(GT="het")>=6' ~/SzpiechLab/abc6435/WarblerROH/vcf/kirtlandii.vcf.gz -Oz -o ~/SzpiechLab/abc6435/WarblerROH/vcf/kirtlandii_ExHet.vcf.gz
--------------------------------------------------END OF FILE---------------------------------------------
qsub filterExHet_kirt.bash
```

```bash
#Execute Filter
nano filterExHet_rut.bash
------------------------------------------------------NANO------------------------------------------------
#!/bin/bash
#PBS -l feature=rhel7
#PBS -l nodes=1:ppn=1
#PBS -l walltime=30:00:00
#PBS -l mem=200gb
#PBS -A wff3_a_g_hc_default

#ruticilla filter excess heterozygotes
bcftools view -e 'COUNT(GT="het")>=6' ~/SzpiechLab/abc6435/WarblerROH/vcf/ruticilla.vcf.gz -Oz -o ~/SzpiechLab/abc6435/WarblerROH/vcf/ruticilla_ExHet.vcf.gz
--------------------------------------------------END OF FILE---------------------------------------------
qsub filterExHet_rut.bash
```

