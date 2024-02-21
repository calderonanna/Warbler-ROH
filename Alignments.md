# Alignment Pipeline 
We will be using bowtie2 with a loop to align our reads to the Myrtle Warbler reference genome following the format below:

```bash
<filepath/bowtie2> -p <#threads> \ --very-sensitive-local --local -N 0 --phred33 \ -x <reference_genome_filepath> \ --rg-id <sample_ID_tag> --rg SM:<sample_ID_tag> \ -1 <filepath_reads_1.fastq> -2 <filepath_reads_2.fastq> -S <filepath/output.sam> \ >& <filepath/output.log>
```

 - **-p** : If your computer has multiple processors/cores specify the number; the default=1. 
**--very-sensitive -local**: Same as -D 20 -R 3 -N 0 -L 20 -i S,1,0.50
   
**--local**: characters may be omitted ("soft clipped") from the ends to achieve the greatest possible alignment score.

**--N**: Sets the number of mismatches to allowed in a seed alignment during multiseed alignment. Default=0, increasing it slows down the alignment but increases sensitivity. 
**--phred33**: Input qualities are ASCII chars equal to the Phred+33 encoding (used by Illumina pipelines).
**-x**: Filepath to the indexed reference genome
**--rg-id**: This causes the SAM @RG header line to be printed, with <text> as the value associated with the ID: tag
**--rg SM:**: Add <text> as a field on the @RG header line. In order for the @RG line to appear, --rg-id must be specified.
**-1**: file containing mate 1 reads
**-2**: file containing mate 2 reads
**-U**: comma-separated list of files containing unpaired reads to be aligned
**-X**: The maximum gap length for valid paired-end alignments.  
	- For example in -X 20, if the gap between R1 and R2 exceeds 20, those alignments are not valid. If the 		gap between R1 and R2 is less than 20, that's acceptable. 
 ```bash
#-----10bp------>
#====================================50bp==========================
#-												   <------10pp-----
#The alignment above would be invalid because the gap is at 30 and exceeds -X 20. 
```

**-S**: the output alignment in SAM format. 
**>&**: the pathway for where to store log output for the alignment

Note: When doing alignments for WGS, they will require a-lot of memory between 200-400gb, so be sure to request this much in the pbs script, otherwise, the alignments may not be done properly. Another thing, do not send the alignment pipeline through parallel. Each sample requires 4 nodes, so parallel will quickly eat up all the available nodes and memory. Write a script for each sample and send the scripts independently so the cluster can allocate the appropriate resources to each sample without maxing out. 

## Alignment Scripts Batch 1
```bash
#Create an alignment script for samples in batch 1
for i in `cat ~/SzpiechLab/abc6435/Scripts/warblerID_batch1.txt`; do echo "
#!/bin/bash
#PBS -l feature=rhel7
#PBS -l nodes=1:ppn=4
#PBS -l walltime=336:00:00
#PBS -l mem=200gb
#PBS -A zps5164_a_g_hc_default

/storage/work/dut374/bin/bowtie2-2.3.5.1/bowtie2 -p 4 \ --very-sensitive-local --local -N 0 --phred33 -x /gpfs/group/dut374/default/mywa_genome_2/final_assembly/mywagenomev2.1 --rg-id ${i} --rg SM:${i} -1 /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/${i}/${i}_trimmed.pair1.truncated.gz -2 /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/${i}/${i}_trimmed.pair2.truncated.gz -U /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/${i}/${i}_trimmed.collapsed.gz -X 700 -S /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/${i}/${i}.sam >& /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/${i}/${i}.log" >> /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/${i}/${i}_align.bash; done

#Submit each alignmentscript to the queue
for i in `cat ~/SzpiechLab/abc6435/Scripts/warblerID_batch1.txt`; do qsub /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/${i}/${i}_align.bash; done
```

### Manuscript Methods
We aligned all read data to the Myrtle Warbler (Setophaga coronata coronata) reference genome (Baiz et al. 2021) using the tool BowTie2 (Langmead and Salzberg 2012) with the following parameters: "very-sensitive-local" and "-X", the maximum gap length for valid paired-end alignments, to 700bp. 
