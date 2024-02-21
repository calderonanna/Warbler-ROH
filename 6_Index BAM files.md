### Index Marked BAM files
Create an indexing script for each of the 21 samples.
```bash
for i in `cat ~/SzpiechLab/abc6435/Scripts/warbler_id_list.txt`; do echo "
#!/bin/bash
#PBS -l feature=rhel7
#PBS -l nodes=1:ppn=1
#PBS -l walltime=336:00:00
#PBS -l mem=100gb
#PBS -A zps5164_a_g_hc_default

samtools index ~/SzpiechLab/abc6435/WarblerROH/${i}/${i}_marked.bam ~/SzpiechLab/abc6435/WarblerROH/${i}/${i}_marked.bai" >> ~/SzpiechLab/abc6435/WarblerROH/${i}/${i}_index.bash; done

#Submit each of the indexing scripts to the queue
for i in `cat ~/SzpiechLab/abc6435/Scripts/warbler_id_list.txt`; do qsub ~/SzpiechLab/abc6435/WarblerROH/${i}/${i}_index.bash; done
```


