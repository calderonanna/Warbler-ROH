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
bcftools index Setophaga.vcf.gz

#Count Number of Sites
zcat Setophaga.vcf.gz | cut -f 1 | grep -v '^#' | wc -l
#Output: 90715651
```

## Filter 1: Read Depth
Filter 1: Sites with **combined** read depth across individuals less than 255 or greater than 401 were filtered out (these values correspond to 5th and 95th percentiles for total depth of our samples, respectively)
```bash
#test (pass)
qsub -A zps5164_a_g_hc_default -l feature=rhel7 -l walltime=10:00:00 -l nodes=1:ppn=1 -l mem=100gb -I
zcat Setophaga.vcf.gz | head -2300 > test.vcf
bcftools view -e 'SUM(FMT/DP)<255 | SUM(FMT/DP)>401' test.vcf -Oz -o test_DP.vcf.gz

#Execute Filter
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

#Count Number of Sites
bcftools view --no-header Setophaga_DP.vcf.gz | grep 'chr' | wc -l
#Output: 
```
## Filter2: Site Quality 
```bash
#test (pass)
qsub -A zps5164_a_g_hc_default -l feature=rhel7 -l walltime=10:00:00 -l nodes=1:ppn=1 -l mem=100gb -I
bcftools view -i '%QUAL>=50' test_DP.vcf.gz -Oz -o test_QUAL.vcf.gz

#Execute Filter
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
```

## Filter3: ADF/ADR (Skipped)
 Sites required at least 2 of the same alternate allele observations on each of the forward and reverse strands.  Note: My vcf file doesn't have this  informations in the header so, I'm skipping this for now. I will have to go back to HaplotypeCaller Step to specify that I want the forward and reverse allele depth annotation. 
```bash
#test (fail)
bcftools view -i 'FMT/AD[*:0] > 1 && FMT/AD[*:1] > 1' test_QUAL.vcf.gz -Oz -o test_ADR_ADF.vcf.gz 
```

## Filter4: Biallelic Sites
Multi-nucleotide polymorphisms, indels, complex variants, and sites with more than one alternate allele were excluded, as were sites with more than two missing genotypes. (See https://www.biostars.org/p/141156/)
```bash
#test (pass)
bcftools view -m2 -M2 -v snps test_QUAL.vcf.gz -Oz -o test_biallelic.vcf.gz

#Execute Filter
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
## Filter5: Excess Heterozygosity
Finally, sites with excess heterozygosity per species, defined as sites with six or more heterozygote genotypes called out of the seven individuals (7 individuals per species), were excluded.  

```bash
#test (pass, pass)
#Subset the VCF by species-S.citrina
bcftools view -s 262,2871,TE22T01,TE30T02,SE25T02 test_biallelic.vcf.gz -Oz -o citrina.vcf.gz
bcftools view -e 'COUNT(GT="het")>=6' citrina.vcf.gz -Oz -o citrina_ExHet.vcf.gz

#Subset species 
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


#Execute Filter
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

#Execute Filter
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

