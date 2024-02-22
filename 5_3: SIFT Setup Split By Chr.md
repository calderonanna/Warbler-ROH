# SIFT 4G Database: Splitting
Now that we have a copy of annotations file and our reference genome, we are going to split these files by chromosomes. The reason for this is to speed up the process of building the database. Instead of making one big database for the entire genome, which will take way too long, we will make as many "mini" databases as there are chromosomes and then fuse all those into one coherent database at the very end. 

## Split Annotations File 
✎Note✎: When you get to the step where you run SIFT, you have to zip up the .gtf files. I do later down the line.

```bash
qsub -A zps5164_a_g_hc_default -l feature=rhel7 -l walltime=10:00:00 -l nodes=1:ppn=1 -l mem=100gb -I 

cd ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/AnnotationsSplit 

#Chrm1
grep -w "chr1" mywagenomev2.1.gene.gtf > mywagenomev2.1.gene_chr1.gtf
#Chrm1a
grep -w "chr1a" mywagenomev2.1.gene.gtf > mywagenomev2.1.gene_chr1a.gtf
#Chrm4a
grep -w "chr4a" mywagenomev2.1.gene.gtf > mywagenomev2.1.gene_chr4a.gtf
#Chrm2-4
for i in {2..4}; do grep -w "chr$i" mywagenomev2.1.gene.gtf >  "mywagenomev2.1.gene_chr$i.gtf"; done
#Chrm5-29
for i in {5..29}; do grep -w "chr$i" mywagenomev2.1.gene.gtf >  "mywagenomev2.1.gene_chr$i.gtf"; done
```
## Split Reference Genome 
You can split the reference genome in several ways. Camille used csplit with the following code:
 `csplit -s -z ref.fa "/>chr[1-2]*[0-9]\b/" '{*}'`. 
 However, this wasn't working for me. You could also use grep:
 `for i in {1..29}; do less -S mywagenomev2.1.fa | grep -w -A1 \>chr$i > chr${i}.fa; done`. 
  The other option is to use samtools
  `for i in {1..29}; do samtools faidx mywagenomev2.1.fa chr${i} > mywagenomev2.1.chr${i}.fa`. 
  I decided to go with samtools just to be safe. 
  
```bash
qsub -A zps5164_a_g_hc_default -l feature=rhel7 -l walltime=10:00:00 -l nodes=1:ppn=1 -l mem=100gb -I 

cd ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/GenomeSplit
conda activate bioinfo

#Chr 1a and 4a
samtools faidx mywagenomev2.1.fa chr1a > mywagenomev2.1.chr1a.fa
samtools faidx mywagenomev2.1.fa chr4a > mywagenomev2.1.chr4a.fa
#Chr 1-29
for i in {1..29}; do samtools faidx mywagenomev2.1.fa chr${i} > mywagenomev2.1.chr${i}.fa; done
#Remove chr16 (does not exist in Warblers)
rm mywagenomev2.1.chr16.fa
```

