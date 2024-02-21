
# Convert SAM alignment files to BAM format
In order to do anything meaningful, we need to convert our SAM files to binary format (BAM). We can accomplish this using SAMTOOLS, which is already pre-installed into the cluster. Specifically, we will use the samtools view command whose function is to convert between human and computer readable formats. We must specify that our input is in SAM format (by default it expects BAM) using the -S option. We must also say that we want the output to be BAM (by default it produces BAM) with the -b option. SAMTOOLS follows the UNIX convention of sending its output to the UNIX STDOUT, so we need to use a redirect operator (“>”) to create a BAM file from the output. http://quinlanlab.org/tutorials/samtools/samtools.html#converting-sam-to-bam-with-samtools-view
`
samtools view -S -b sample.sam > sample.bam
`
**-S**: specifies that the input file is a sam file
**-b**: species that we want to produce a bam file
**>**: denotes where to store the output bam file. 

## Convert Batch1 
```bash
#create a script for all samples in batch 1
for i in `cat ~/SzpiechLab/abc6435/Scripts/warblerID_batch1.txt`; do echo "
#!/bin/bash
#PBS -l feature=rhel7
#PBS -l nodes=1:ppn=1
#PBS -l walltime=336:00:00
#PBS -l mem=100gb
#PBS -A zps5164_a_g_hc_default

samtools view -S -b ~/SzpiechLab/abc6435/WarblerROH/${i}/${i}.sam > ~/SzpiechLab/abc6435/WarblerROH/${i}/${i}.bam" >> ~/SzpiechLab/abc6435/WarblerROH/${i}/${i}_convert.bash; done

#Submit each of the convert scripts to the queue
for i in `cat ~/SzpiechLab/abc6435/Scripts/warblerID_batch1.txt`; do qsub ~/SzpiechLab/abc6435/WarblerROH/${i}/${i}_convert.bash; done
```
## Convert Batch2
```bash
#create a script for all samples in batch 2
for i in `cat ~/SzpiechLab/abc6435/Scripts/warblerID_batch2.txt`; do echo "
#!/bin/bash
#PBS -l feature=rhel7
#PBS -l nodes=1:ppn=1
#PBS -l walltime=336:00:00
#PBS -l mem=200gb
#PBS -A wff3_a_g_hc_default

samtools view -S -b ~/SzpiechLab/abc6435/WarblerROH/${i}/${i}.sam > ~/SzpiechLab/abc6435/WarblerROH/${i}/${i}.bam" >> ~/SzpiechLab/abc6435/WarblerROH/${i}/${i}_convert.bash; done

#Submit each of the convert scripts to the queue
for i in `cat ~/SzpiechLab/abc6435/Scripts/warblerID_batch2.txt`; do qsub ~/SzpiechLab/abc6435/WarblerROH/${i}/${i}_convert.bash; done
```



