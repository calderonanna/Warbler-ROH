# Creating a SIFT 4G Database: Set Up
https://www.nature.com/articles/nprot.2015.123
https://github.com/pauline-ng/SIFT4G_Create_Genomic_DB
Sift4G is a program that uses sequence conservation to predict whether amino acid substitutions are deleterious or tolerated. To do this, it employs a multistep (seed detection algorithm and Smith-Waterman algorithm) to search a query sequence against a large database. When the program finds matches between the query and a sequence in the database, similarity scores are calculated between all matching sequences. Top 5,000 scoring hits are  passed through the second algorithm (Smith-Waterman) which reconstructs the query against the top scoring hits and chooses 400 hits with the most significant p-values. 

## Obtain Copy of Your Ref Genome
To make things easier, I obtained a copy of the reference genome and put it into a folder called GenomeSplit. The GenomeSplit name will make sense in the next step. 
```bash
nano copyRefGenome.bash
------------------------------------------------NANO------------------------------------------------------
#!/bin/bash
#!/bin/bash
#PBS -l feature=rhel7
#PBS -l nodes=1:ppn=1
#PBS -l walltime=36:00:00
#PBS -l mem=200gb
#PBS -A zps5164_a_g_hc_default

#Make Directory and copy ref genome into it
cd ~/SzpiechLab/abc6435/WarblerROH/SIFT4G
mkdir GenomeSplit
cp /gpfs/group/dut374/default/mywa_genome_2/final_assembly/mywagenomev2.1.fa ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/GenomeSplit

#Decompress the ref genome if it is zipped
#bgzip -d mywagenomev2.1.fa.gz
------------------------------------------END-OF-SCRIPT---------------------------------------------------
qsub copyRefGenome.bash

```
## Obtain Copy of Annotations 
I also obtained a copy of the annotations file and put it into a folder called AnnotationsSplit, which will make more sense in Step 2 why I did it this way.
```bash
nano copyAnnotations.bash
------------------------------------------------NANO------------------------------------------------------
#!/bin/bash
#!/bin/bash
#PBS -l feature=rhel7
#PBS -l nodes=1:ppn=1
#PBS -l walltime=6:00:00
#PBS -l mem=200gb
#PBS -A zps5164_a_g_hc_default

#Make a directory and copy annotations file into it
cd ~/SzpiechLab/abc6435/WarblerROH/SIFT4G
mkdir AnnotationsSplit 
cp /gpfs/group/dut374/default/mywa_genome_2/annotation/mywagenomev2.1.all.noseq.gff ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/AnnotationsSplit
------------------------------------------END-OF-SCRIPT---------------------------------------------------
qsub copyAnnotations.bash
```
## Convert Annotations File to gtf
The annotations file needs to be in gtf format and will be converted with the program gffread.

```bash
#Install gffread
conda activate bioinfo 
conda install -c bioconda gffread

#Convert annotations.gff into .gtf format
qsub -A zps5164_a_g_hc_default -l feature=rhel7 -l walltime=10:00:00 -l nodes=1:ppn=1 -l mem=100gb -I 

conda activate bioinfo
cd ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/AnnotationsSplit 
gffread mywagenomev2.1.all.noseq.gff -T -o mywagenomev2.1.gene.gtf
```
