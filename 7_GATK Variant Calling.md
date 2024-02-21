
# Genomic Analysis Tool Kit (GATK)
In this step we will use GATK to call genotypes on all positions and produce a GVCF file. To do this we will use the program HaplotypeCaller. The program will look at the bam file and identify regions that vary using sliding windows. It will determine plausible haplotypes, determine per-read likelihood haplotypes, and assign each sample with genotypes, likelihood, and annotations. 
![Source: Broad Institute](https://us.v-cdn.net/5019796/uploads/FileUpload/a4/5ac06fc8af4b1b0c474f03e45f9017.png)

Variant Calling with GATK useful links:
https://www.youtube.com/watch?v=hRsjy1Z8QDA
https://gatk.broadinstitute.org/ 
https://sites.google.com/a/broadinstitute.org/legacy-gatk-forum-discussions/methods-and-algorithms/4148-HC-overview-How-the-HaplotypeCaller-works
https://github.com/broadinstitute/gatk-docs/blob/master/gatk3-faqs/What_is_a_GVCF_and_how_is_it_different_from_a_'regular'_VCF%3F.md#:~:text=The%20key%20difference%20between%20a,a%20cohort%20in%20subsequent%20steps.

## HaplotypeCaller 
`gatk --java-options -Xmx4G HaplotypeCaller -R reference.fasta -I sample.bam -O sample.g.vcf.gz -bamout sample.out.bam -ERC GVCF`

**-Xmx**: Specify Java memory argument

**-R**: Specify the reference genome (*.fasta)

**-I**: Specify BAM Input (*.bam)

**-O**: Specify the output file name (*.g.vcf)

**-ERC GVCF**: Use GVCF as output: https://gatk.broadinstitute.org/hc/en-us/articles/360035531812-GVCF-Genomic-Variant-Call-Format#:~:text=The%20two%20types%20of%20GVCFs,or%20a%20non%2Dvariant%20record.

**-bamout**: Specify the reassembled bam file name. HaplotypeCaller performs a local reassembly and realignment of the reads in the region surrounding a potential variant. During the calling process, reads get moved to different mapping positions that what the original BAM files describe. -bamout flag enables you to visualize what rearrangements the tool has made.

**-L #**: Specify interval, if any (eg. -L 20). In other words, if you're only calling variants for a specific chromosome, you need to specify what chromosome it is. In this example, they are only looking to call variants on chromosome 20, so -L 20. If looking at all chromosomes, do not specify. 

## SNP Calling Script
Create SNP Calling script for each of the 21 samples. 

```bash
for i in `cat ~/SzpiechLab/abc6435/Scripts/warbler_id_list.txt`; do echo "
#!/bin/bash
#PBS -l feature=rhel7
#PBS -l nodes=1:ppn=40
#PBS -l walltime=100:00:00
#PBS -l mem=500gb
#PBS -A wff3_a_g_hc_default

java -Xmx500g -jar /storage/work/dut374/bin/GenomeAnalysisTK-3.8/GenomeAnalysisTK.jar -T HaplotypeCaller -R /gpfs/group/dut374/default/mywa_genome_2/final_assembly/mywagenomev2.1.fa -I ~/SzpiechLab/abc6435/WarblerROH/${i}/${i}_marked.bam -nct 40 --emitRefConfidence GVCF -o ~/SzpiechLab/abc6435/WarblerROH/${i}/${i}.g.vcf >& ~/SzpiechLab/abc6435/WarblerROH/${i}/${i}gvcf.log" >> ~/SzpiechLab/abc6435/WarblerROH/${i}/${i}snpcall.bash; done
```
## ZIP GVCF files
`nohup bgzip sample.g.vcf`
```bash
qsub -A wff3_a_g_hc_default -l feature=rhel7 -l walltime=30:00:00 -l nodes=1:ppn=20 -l mem=200gb -I

nohup bgzip ~/SzpiechLab/abc6435/WarblerROH/163/163.g.vcf &
nohup bgzip ~/SzpiechLab/abc6435/WarblerROH/262/262.g.vcf &
nohup bgzip ~/SzpiechLab/abc6435/WarblerROH/1049/1049.g.vcf &
nohup bgzip ~/SzpiechLab/abc6435/WarblerROH/1940/1940.g.vcf &
nohup bgzip ~/SzpiechLab/abc6435/WarblerROH/2871/2871.g.vcf &
nohup bgzip ~/SzpiechLab/abc6435/WarblerROH/4056/4056.g.vcf &
nohup bgzip ~/SzpiechLab/abc6435/WarblerROH/104049/104049.g.vcf &
nohup bgzip ~/SzpiechLab/abc6435/WarblerROH/107057/107057.g.vcf &
nohup bgzip ~/SzpiechLab/abc6435/WarblerROH/183195332/183195332.g.vcf &
nohup bgzip ~/SzpiechLab/abc6435/WarblerROH/183194861/183194861.g.vcf &
nohup bgzip ~/SzpiechLab/abc6435/WarblerROH/183195321/183195321.g.vcf &
nohup bgzip ~/SzpiechLab/abc6435/WarblerROH/183195304/183195304.g.vcf &
nohup bgzip ~/SzpiechLab/abc6435/WarblerROH/183194841/183194841.g.vcf &
nohup bgzip ~/SzpiechLab/abc6435/WarblerROH/284029323/284029323.g.vcf &
nohup bgzip ~/SzpiechLab/abc6435/WarblerROH/TE22T01/TE22T01.g.vcf &
nohup bgzip ~/SzpiechLab/abc6435/WarblerROH/TE30T02/TE30T02.g.vcf &
nohup bgzip ~/SzpiechLab/abc6435/WarblerROH/SE25T02/SE25T02.g.vcf &
nohup bgzip ~/SzpiechLab/abc6435/WarblerROH/TF19T04/TF19T04.g.vcf &
nohup bgzip ~/SzpiechLab/abc6435/WarblerROH/TF03T03/TF03T03.g.vcf &
nohup bgzip ~/SzpiechLab/abc6435/WarblerROH/183195326/183195326.g.vcf &
nohup bgzip ~/SzpiechLab/abc6435/WarblerROH/183195312/183195312.g.vcf &

```
## Index the zipped GVCF files
`nohup tabix sample.g.vcf.gz`
```bash
nohup tabix ~/SzpiechLab/abc6435/WarblerROH/163/163.g.vcf.gz &
nohup tabix ~/SzpiechLab/abc6435/WarblerROH/262/262.g.vcf.gz &
nohup tabix ~/SzpiechLab/abc6435/WarblerROH/1049/1049.g.vcf.gz &
nohup tabix ~/SzpiechLab/abc6435/WarblerROH/1940/1940.g.vcf.gz &
nohup tabix ~/SzpiechLab/abc6435/WarblerROH/2871/2871.g.vcf.gz &
nohup tabix ~/SzpiechLab/abc6435/WarblerROH/4056/4056.g.vcf.gz &
nohup tabix ~/SzpiechLab/abc6435/WarblerROH/104049/104049.g.vcf.gz &
nohup tabix ~/SzpiechLab/abc6435/WarblerROH/107057/107057.g.vcf.gz &
nohup tabix ~/SzpiechLab/abc6435/WarblerROH/183195332/183195332.g.vcf.gz &
nohup tabix ~/SzpiechLab/abc6435/WarblerROH/183194861/183194861.g.vcf.gz &
nohup tabix ~/SzpiechLab/abc6435/WarblerROH/183195321/183195321.g.vcf.gz &
nohup tabix ~/SzpiechLab/abc6435/WarblerROH/183195304/183195304.g.vcf.gz &
nohup tabix ~/SzpiechLab/abc6435/WarblerROH/183194841/183194841.g.vcf.gz &
nohup tabix ~/SzpiechLab/abc6435/WarblerROH/284029323/284029323.g.vcf.gz &
nohup tabix ~/SzpiechLab/abc6435/WarblerROH/TE22T01/TE22T01.g.vcf.gz &
nohup tabix ~/SzpiechLab/abc6435/WarblerROH/TE30T02/TE30T02.g.vcf.gz &
nohup tabix ~/SzpiechLab/abc6435/WarblerROH/SE25T02/SE25T02.g.vcf.gz &
nohup tabix ~/SzpiechLab/abc6435/WarblerROH/TF19T04/TF19T04.g.vcf.gz &
nohup tabix ~/SzpiechLab/abc6435/WarblerROH/TF03T03/TF03T03.g.vcf.gz &
nohup tabix ~/SzpiechLab/abc6435/WarblerROH/183195326/183195326.g.vcf.gz &
nohup tabix ~/SzpiechLab/abc6435/WarblerROH/183195312/183195312.g.vcf.gz &
```
## Calling SNPS across all GVCFs
Note to self: I will need to come back to this step to include the DP4(ADF and ADR) flag. For now, I will skip including this flag because of time constraints. 

`java -Xmx500g -jar /storage/work/dut374/bin/GenomeAnalysisTK-3.8/GenomeAnalysisTK.jar -T GenotypeGVCFs -R reference.fa -nt 40 -V sample1.g.vcf.gz -V sample2.g.vcf.gz -o filename.vcf >& filename.log`

```bash
qsub -A wff3_a_g_hc_default -l feature=rhel7 -l walltime=30:00:00 -l nodes=1:ppn=40 -l mem=500gb -I

#!/bin/bash
#PBS -l feature=rhel7
#PBS -l nodes=1:ppn=40
#PBS -l walltime=100:00:00
#PBS -l mem=500gb
#PBS -A wff3_a_g_hc_default

java -Xmx500g -jar /storage/work/dut374/bin/GenomeAnalysisTK-3.8/GenomeAnalysisTK.jar -T GenotypeGVCFs -R /gpfs/group/dut374/default/mywa_genome_2/final_assembly/mywagenomev2.1.fa -nt 40 -V /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/163/163.g.vcf.gz -V /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/262/262.g.vcf.gz -V /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/1049/1049.g.vcf.gz -V /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/1940/1940.g.vcf.gz -V /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/2871/2871.g.vcf.gz -V /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/4056/4056.g.vcf.gz -V /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/104049/104049.g.vcf.gz -V /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/107057/107057.g.vcf.gz -V /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/183195332/183195332.g.vcf.gz -V /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/183194861/183194861.g.vcf.gz -V /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/183195321/183195321.g.vcf.gz -V /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/183195304/183195304.g.vcf.gz -V /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/183194841/183194841.g.vcf.gz -V /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/284029323/284029323.g.vcf.gz -V /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/TE22T01/TE22T01.g.vcf.gz -V /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/TE30T02/TE30T02.g.vcf.gz -V /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/SE25T02/SE25T02.g.vcf.gz -V /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/TF19T04/TF19T04.g.vcf.gz -V /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/TF03T03/TF03T03.g.vcf.gz -V /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/183195326/183195326.g.vcf.gz -V /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/183195312/183195312.g.vcf.gz -o /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/Setophaga.vcf >& /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/varcall.log
```

