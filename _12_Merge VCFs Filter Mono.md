# Merge VCFs Filter Monomorphic Sites
Zach recombined the following files `kirtlandii_filtered.vcf.gz`, `citrina_filtered.vcf.gz`, `ruticilla_filtered.vcf.gz` back into one vcf called `setophaga_filtered_isec.vcf.gz`. But this file still contains alot of monomorphic data. In other words, lots of our sites have 1/1 (alternate/alternate) genotypes because our data was aligned to a reference genome that was a different species. Thus, these sites need to be filtered out. 

`bcftools view input.vcf.gz -c 1:minor`
* Keeps sites where this is at least 1 copy of the minor allele 
* In the example below, T is our minor allele

|T|A| Keep?
|--|--|--|
|10|10|Y
|9|11|Y
|1|19|Y
|0|20|N

## Filter Monomorphic sites from merged vcf files
```bash 
qsub -A zps5164_a_g_sc_default -l feature=rhel7 -l walltime=10:00:00 -l nodes=1:ppn=1 -l mem=100gb -I
cd SzpiechLab/abc6435/WarblerROH/vcf/Setophaga/
bcftools view setophaga_filtered_isec.vcf.gz -c 1:minor -Oz -o setophaga_filtered_isec_nomono.vcf.gz

#Count Number of Sites
cat setophaga_filtered_isec_nomono.vcf | cut -f 1 | grep -v '^#' | wc -l
#output: 50701237
```
### Note
To only look at sites where all samples are either AA or RR
`bcftools view -i 'COUNT(GT="AA")=N_SAMPLES' header.vcf | wc -l`
