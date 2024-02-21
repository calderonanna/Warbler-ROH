
# Sorting BAM files
When you align FASTQ files with all current sequence aligners, the alignments produced are in random order with respect to their position in the reference genome. In other words, the BAM file is in the order that the sequences occurred in the input FASTQ files. Doing anything meaningful such as calling variants or visualizing alignments in IGV requires that the BAM is "organized". It must be sorted such that the alignments occur in “genome order”. That is, ordered positionally based upon their alignment coordinates on each chromosome. As I did previously, I split my samples into two groups to speed things up. 

`samtools sort sample.sam -T sample_temp.bam -o sample_sorted.bam`

**-T**: Write temporary files

**-o**: Write the final sorted output to the specified file (sample_sorted.bam), rather than to standard output.

## Sort Batch1 
```bash
#create a sort script for all samples in batch 1
for i in `cat ~/SzpiechLab/abc6435/Scripts/warblerID_batch1.txt`; do echo "
#!/bin/bash
#PBS -l feature=rhel7
#PBS -l nodes=1:ppn=1
#PBS -l walltime=336:00:00
#PBS -l mem=100gb
#PBS -A zps5164_a_g_hc_default

samtools sort ~/SzpiechLab/abc6435/WarblerROH/${i}/${i}.bam -T ~/SzpiechLab/abc6435/WarblerROH/${i}/${i}_temp.bam -o ~/SzpiechLab/abc6435/WarblerROH/${i}/${i}_sorted.bam" >> ~/SzpiechLab/abc6435/WarblerROH/${i}/${i}_sort.bash; done

#Submit each of the sort scripts to the queue
for i in `cat ~/SzpiechLab/abc6435/Scripts/warblerID_batch1.txt`; do qsub ~/SzpiechLab/abc6435/WarblerROH/${i}/${i}_sort.bash; done
```
## Sort Batch2
```bash
#create a sort script for all samples in batch 2
for i in `cat ~/SzpiechLab/abc6435/Scripts/warblerID_batch2.txt`; do echo "
#!/bin/bash
#PBS -l feature=rhel7
#PBS -l nodes=1:ppn=1
#PBS -l walltime=336:00:00
#PBS -l mem=100gb
#PBS -A wff3_a_g_hc_default

samtools sort ~/SzpiechLab/abc6435/WarblerROH/${i}/${i}.bam -T ~/SzpiechLab/abc6435/WarblerROH/${i}/${i}_temp.bam -o ~/SzpiechLab/abc6435/WarblerROH/${i}/${i}_sorted.bam" >> ~/SzpiechLab/abc6435/WarblerROH/${i}/${i}_sort.bash; done

#Submit each of the sort scripts to the queue
for i in `cat ~/SzpiechLab/abc6435/Scripts/warblerID_batch2.txt`; do qsub ~/SzpiechLab/abc6435/WarblerROH/${i}/${i}_sort.bash; done
```




