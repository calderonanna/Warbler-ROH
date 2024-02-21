

# Marking Duplicated Reads In Sorted BAM Files
Marking duplicates tool identifies reads that are identical (or duplicated). Unless you specify, MarkDuplicates does not remove the duplicates. Rather, it puts a little flag on it (or marks it, duh). Although you can request for MarkDuplicates to remove them, that will reduce the coverage. The reason we flag them instead of removing them is so that downstream GATK tools can read the flagged duplicates and ignore the reads, and most GATK tools will ignore the duplicates by default. 

`java -jar picard.jar MarkDuplicates I=input.bam O=marked_duplicates.bam M=marked_dup_metrics.txt MAX_FILE_HANDLES_FOR_READ_ENDS_MAP=8000`

**I**:One or more input SAM or BAM files to analyze. Must be coordinate sorted. Default value: null. This option may be specified 0 or more times.

**O**: The output file to write marked records to required.

**METRICS_FILE**: File to write duplication metrics required.

**MAX_FILE_HANDLES_FOR_READ_ENDS_MAP=8000**: Maximum number of file handles to keep open when spilling read ends to disk. Set this number a little lower than the per-process maximum number of file that may be open. This number can be found by executing the 'ulimit -n' command on a Unix system. Default value: 8000. This option can be set to 'null' to clear the default value.

## MarkDups Batch1
```bash
#create a mark script for all samples in batch 1
for i in `cat ~/SzpiechLab/abc6435/Scripts/warblerID_batch1.txt`; do echo "
#!/bin/bash
#PBS -l feature=rhel7
#PBS -l nodes=1:ppn=1
#PBS -l walltime=336:00:00
#PBS -l pmem=300gb
#PBS -A wff3_a_g_hc_default

java -Xmx300g -jar /storage/work/dut374/bin/picard_tools_2.20.8/picard.jar  MarkDuplicates INPUT=~/SzpiechLab/abc6435/WarblerROH/${i}/${i}_sorted.bam OUTPUT=~/SzpiechLab/abc6435/WarblerROH/${i}/${i}_marked.bam METRICS_FILE=~/SzpiechLab/abc6435/WarblerROH/${i}/${i}_metrics.txt MAX_FILE_HANDLES_FOR_READ_ENDS_MAP=8000" >> ~/SzpiechLab/abc6435/WarblerROH/${i}/${i}_mark.bash; done

#Submit each of the mark scripts to the queue
for i in `cat ~/SzpiechLab/abc6435/Scripts/warblerID_batch1.txt`; do qsub ~/SzpiechLab/abc6435/WarblerROH/${i}/${i}_mark.bash; done
```
## MarkDups Batch2
```bash
#create a mark script for all samples in batch 2
for i in `cat ~/SzpiechLab/abc6435/Scripts/warblerID_batch2.txt`; do echo "
#!/bin/bash
#PBS -l feature=rhel7
#PBS -l nodes=1:ppn=1
#PBS -l walltime=336:00:00
#PBS -l pmem=300gb
#PBS -A wff3_a_g_hc_default

java -Xmx300g -jar /storage/work/dut374/bin/picard_tools_2.20.8/picard.jar  MarkDuplicates INPUT=~/SzpiechLab/abc6435/WarblerROH/${i}/${i}_sorted.bam OUTPUT=~/SzpiechLab/abc6435/WarblerROH/${i}/${i}_marked.bam METRICS_FILE=~/SzpiechLab/abc6435/WarblerROH/${i}/${i}_metrics.txt MAX_FILE_HANDLES_FOR_READ_ENDS_MAP=8000" >> ~/SzpiechLab/abc6435/WarblerROH/${i}/${i}_mark.bash; done

#Submit each of the sort scripts to the queue
for i in `cat ~/SzpiechLab/abc6435/Scripts/warblerID_batch2.txt`; do qsub ~/SzpiechLab/abc6435/WarblerROH/${i}/${i}_mark.bash; done
```

QUESTION: Should I also do this part --> "We quantified  genomic coverage using “qualimap” (Okonechnikov et al. 2016)."
