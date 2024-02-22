
# VCF Filter Z and Scaffolds
Remove all unmapped scaffolds and the z-chromosome

```bash
nano subset_vcfs.bash
------------------------------------------------------NANO------------------------------------------------
#!/bin/bash
#PBS -l feature=rhel7
#PBS -l nodes=1:ppn=1
#PBS -l walltime=15:00:00
#PBS -l mem=200gb
#PBS -A wff3_a_g_hc_default

cd ~/SzpiechLab/abc6435/WarblerROH/vcf
#Index 
bcftools index citrina_FMISSING.vcf.gz
bcftools index ruticilla_FMISSING.vcf.gz
bcftools index kirtlandii_FMISSING.vcf.gz

#citrina - Remove scaffolds and chr z
bcftools view citrina_FMISSING.vcf.gz --regions chr1,chr1a,chr2,chr3,chr4,chr4a,chr5,chr6,chr7,chr8,chr9,chr10,chr11,chr12,chr13,chr14,chr15,chr17,chr18,chr19,chr20,chr21,chr22,chr23,chr24,chr25,chr26,chr27,chr28,chr29 -Oz -o citrina_filtered.vcf.gz

#ruticilla - Remove scaffolds and chr z
bcftools view ruticilla_FMISSING.vcf.gz --regions chr1,chr1a,chr2,chr3,chr4,chr4a,chr5,chr6,chr7,chr8,chr9,chr10,chr11,chr12,chr13,chr14,chr15,chr17,chr18,chr19,chr20,chr21,chr22,chr23,chr24,chr25,chr26,chr27,chr28,chr29 -Oz -o ruticilla_filtered.vcf.gz

#kirtlandii - Remove scaffolds and chr z
bcftools view kirtlandii_FMISSING.vcf.gz --regions chr1,chr1a,chr2,chr3,chr4,chr4a,chr5,chr6,chr7,chr8,chr9,chr10,chr11,chr12,chr13,chr14,chr15,chr17,chr18,chr19,chr20,chr21,chr22,chr23,chr24,chr25,chr26,chr27,chr28,chr29 -Oz -o kirtlandii_filtered.vcf.gz

--------------------------------------------END OF SCRIPT------------------------------------------------
qsub subset_vcfs.bash
```

