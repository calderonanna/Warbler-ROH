# Re-Split Joint VCF Into Species, Re-filter Monomorphic
Going back to setophaga_filtered_isec_nomono.vcf, which removed all monomorphic sites from the joint vcf, now we want to split that vcf into species and re-filter for monomorphic sites per species vcf.


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

bcftools view citrina_filtered_isec_nomono.vcf.gz -c 1:minor -Oz -o citrina_filtered_isec_nomono_nomono.vcf.gz

bcftools view kirtlandii_filtered_isec_nomono.vcf.gz -c 1:minor -Oz -o kirtlandii_filtered_isec_nomono_nomono.vcf.gz

bcftools view ruticilla_filtered_isec_nomono.vcf.gz -c 1:minor -Oz -o ruticilla_filtered_isec_nomono_nomono.vcf.gz
```


## Generate Monomorphic Data  Data Per Species
https://www.seqanswers.com/forum/bioinformatics/bioinformatics-aa/42970-bcftools-will-not-filter-fixed-sites
The obscure forum that saved our lives. 
```bash
salloc -N 1 -n 1 --mem-per-cpu=100GB -t 5:00:00

bcftools view citrina_filtered_isec_nomono.vcf.gz --max-ac 0:minor -Oz -o > citrina_filtered_isec_nomono_monomorphic.vcf.gz

bcftools view ruticilla_filtered_isec_nomono.vcf.gz --max-ac 0:minor -Oz -o > ruticilla_filtered_isec_nomono_monomorphic.vcf.gz

bcftools view kirtlandii_filtered_isec_nomono.vcf.gz --max-ac 0:minor -Oz -o > kirtlandii_filtered_isec_nomono_monomorphic.vcf.gz
```
