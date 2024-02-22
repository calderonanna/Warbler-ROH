# Re-Split Joint VCF Into Species, Re-filter Monomorphic
Going back to setophaga_filtered_isec_nomono.vcf, which removed all monomorphic sites from the joint vcf, now we want to split that vcf into species and re-filter for monomorphic sites per species vcf. We are wondering if this will help with the inflated prediction counts.  

`bcftools view input.vcf.gz -c 1:minor`
* Keeps sites where this is at least 1 copy of the minor allele 
* In the example below, T is our minor allele

|T|A| Keep?
|--|--|--|
|10|10|Y
|9|11|Y
|1|19|Y
|0|20|N


## Split VCF into species
```bash
salloc -N 1 -n 1 --mem-per-cpu=10GB -t 5:00:00

#Subset by kirtlandii samples from setophaga_filtered_isec_nomono.vcf
bcftools view -S /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/scripts/kirtlandii_ids.txt /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/vcf/Setophaga/setophaga_filtered_isec_nomono.vcf.gz -Oz -o /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/vcf/species/kirtlandii_filtered_isec_nomono.vcf.gz

#Subset by kirtlandii samples from setophaga_filtered_isec_nomono.vcf
bcftools view -S /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/scripts/citrina_ids.txt /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/vcf/Setophaga/setophaga_filtered_isec_nomono.vcf.gz -Oz -o /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/vcf/species/citrina_filtered_isec_nomono.vcf.gz

#Subset by ruticilla samples from setophaga_filtered_isec_nomono.vcf
bcftools view -S /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/scripts/ruticilla_ids.txt /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/vcf/Setophaga/setophaga_filtered_isec_nomono.vcf.gz -Oz -o /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/vcf/species/ruticilla_filtered_isec_nomono.vcf.gz
```

## Generate Polymorphic Data

```bash
salloc -N 1 -n 1 --mem-per-cpu=100GB -t 5:00:00.bash
------------------------------------------------------NANO------------------------------------------------
#!/bin/bash
#PBS -l feature=rhel7
#PBS -l nodes=1:ppn=1
#PBS -l walltime=24:00:00
#PBS -l mem=100gb
#PBS -A wff3_a_g_hc_default

cd /storage/home/abc6435/work

bcftools view citrina_filtered_isec_nomono.vcf.gz -c 1:minor -Oz -o citrina_filtered_isec_nomono_nomono.vcf.gz

bcftools view kirtlandii_filtered_isec_nomono.vcf.gz -c 1:minor -Oz -o kirtlandii_filtered_isec_nomono_nomono.vcf.gz

bcftools view ruticilla_filtered_isec_nomono.vcf.gz -c 1:minor -Oz -o ruticilla_filtered_isec_nomono_nomono.vcf.gz
------------------------------------------------------END OF SCRIPT---------------------------------------
```


## Generate Monomorphic Data  Data Per Species
https://www.seqanswers.com/forum/bioinformatics/bioinformatics-aa/42970-bcftools-will-not-filter-fixed-sites
The obscure forum that saved our lives. 
```bash
salloc -N 1 -n 1 --mem-per-cpu=100GB -t 5:00:00

cd ~/SzpiechLab/abc6435/WarblerROH/vcf/species

bcftools view citrina_filtered_isec_nomono.vcf.gz --max-ac 0:minor -Oz -o > citrina_filtered_isec_nomono_monomorphic.vcf.gz

bcftools view ruticilla_filtered_isec_nomono.vcf.gz --max-ac 0:minor -Oz -o > ruticilla_filtered_isec_nomono_monomorphic.vcf.gz

bcftools view kirtlandii_filtered_isec_nomono.vcf.gz --max-ac 0:minor -Oz -o > kirtlandii_filtered_isec_nomono_monomorphic.vcf.gz
```
