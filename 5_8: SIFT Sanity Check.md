# Sanity Check
Before I gather my database, I just want to make sure that SIFT ran correctly and that every chromosome has predictions and that SIFT completed running. 

## Check If SIFT Ran Completely
The output should be 30!
```bash
cd ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/Scripts/job_err_output
grep -E 'Generating SIFT predictions with sequence identity: 100.00%' *.e* | wc -l
```

## Check If SIFT generated predictions
```bash
for i in {1..29}; do ls ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/chr${i}/SIFT_predictions/*.SIFTprediction | wc -l; done
ls ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/chr1a/SIFT_predictions/*.SIFTprediction | wc -l
ls ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/chr4a/SIFT_predictions/*.SIFTprediction | wc -l
```

## Check if SIFT generated the necessary files. 
Because I do not have a chromosome 16, the only files that should be missing are the following:  `chr16.gz, chr16.regions, and chr16_SIFTDB_stats.txt`
If any other files are missing, something went wrong and needs troubleshooting. For example, `chr11.gz, chr11.regions, and chr11_SIFTDB_stats.txt` were all missing despite there being .SIFTprediction files for chr11. This indicated that SIFT did not complete running and did not have sufficient time to generate the necessary files. So, I went back and re-ran the script giving it 360 hours to complete. 

### List .gz files
```bash 
for i in {1..29}; do ls ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/chr${i}/mywagenome_chr${i}/chr${i}.gz; done
ls ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/chr1a/mywagenome_chr1a/chr1a.gz 
ls ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/chr4a/mywagenome_chr4a/chr4a.gz 
```

### List .regions
```bash
for i in {1..29}; do ls ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/chr${i}/mywagenome_chr${i}/chr${i}.regions; done
ls ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/chr1a/mywagenome_chr1a/chr1a.regions 
ls ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/chr4a/mywagenome_chr4a/chr4a.regions
```

### List SIFTDB_stats.txt
```bash
for i in {1..29}; do ls ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/chr${i}/mywagenome_chr${i}/chr${i}_SIFTDB_stats.txt; done
ls ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/chr1a/mywagenome_chr1a/chr1a_SIFTDB_stats.txt
ls ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/chr4a/mywagenome_chr4a/chr4a_SIFTDB_stats.txt
```
