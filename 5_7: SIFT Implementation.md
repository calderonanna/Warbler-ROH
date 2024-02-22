
# Implementing SIFT4G Genome Wide

✏️Note: When I ran the SIFT program I got a strange message in my error log `Use of uninitialized value $aa in string eq at make-single-records-BIOPERL.pl line 274.` I found a thread on Robert Vaser and Pauline's page as other people have run into these same error. 
https://github.com/rvaser/sift4g/issues/5


## Creating Job Scripts
### Chromosomes 1-29
```bash
cd ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/Scripts

#Writing a job script for all chromosomes
for i in {1..14}; do echo "
#!/bin/bash   
#PBS -l feature=rhel7  
#PBS -l nodes=1:ppn=1  
#PBS -l walltime=48:00:00  
#PBS -l mem=100gb  
#PBS -A zps5164_a_g_hc_default 

#Stop if errors
set -ue

#Load gcc module
module load gcc/9.3.1

cd ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/BuildSIFT/scripts_to_build_SIFT_db
perl make-SIFT-db-all.pl -config /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/SIFT4G/chr${i}/mywa_config_${i}.txt" > SIFTDB_${i}.bash; done

#Submitting the 28 scripts 
for i in {1..14} ; do qsub /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/SIFT4G/Scripts/SIFTDB_${i}.bash; done
```
### Chromosome 1a 
```bash
cd ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/Scripts
echo "
#!/bin/bash   
#PBS -l feature=rhel7  
#PBS -l nodes=1:ppn=1  
#PBS -l walltime=36:00:00  
#PBS -l mem=100gb  
#PBS -A zps5164_a_g_hc_default 

#Stop if errors
set -ue

#Load gcc module
module load gcc/9.3.1

cd ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/BuildSIFT/scripts_to_build_SIFT_db
perl make-SIFT-db-all.pl -config /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/SIFT4G/chr1a/mywa_config_1a.txt" >> SIFTDB_1a.bash
```

### Chromosome 4a
```bash
cd ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/Scripts 
echo "
#!/bin/bash   
#PBS -l feature=rhel7  
#PBS -l nodes=1:ppn=1  
#PBS -l walltime=36:00:00  
#PBS -l mem=100gb  
#PBS -A zps5164_a_g_hc_default 

#Stop if errors
set -ue

#Load gcc module
module load gcc/9.3.1

cd ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/BuildSIFT/scripts_to_build_SIFT_db
perl make-SIFT-db-all.pl -config /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/SIFT4G/chr4a/mywa_config_4a.txt" >> SIFTDB_4a.bash
```
