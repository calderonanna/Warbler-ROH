# Database Compilation
Now that we are sure that SIFT generated all the necessary files, we need to gather all the files into one coherent database. This shouldn't be too difficult to do. Copy the following files for each chromosome into the the database folder: `SIFTDB_stats.txt, .regions, .gz` 

## Make a database folder
```bash
#Make a directory for the entire datase
cd ~/SzpiechLab/abc6435/WarblerROH/SIFT4G
mkdir MYWA_SIFT_DATABASE
```
## Copy .gz files
```bash 
for i in {1..29}; do cp ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/chr${i}/mywagenome_chr${i}/chr${i}.gz ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/MYWA_SIFT_DATABASE; done

cp ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/chr1a/mywagenome_chr1a/chr1a.gz ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/MYWA_SIFT_DATABASE

cp ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/chr4a/mywagenome_chr4a/chr4a.gz ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/MYWA_SIFT_DATABASE
```

## Copy .regions
```bash
for i in {1..29}; do cp ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/chr${i}/mywagenome_chr${i}/chr${i}.regions ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/MYWA_SIFT_DATABASE; done

cp ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/chr1a/mywagenome_chr1a/chr1a.regions ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/MYWA_SIFT_DATABASE

cp ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/chr4a/mywagenome_chr4a/chr4a.regions ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/MYWA_SIFT_DATABASE
```

## Copy SIFTDB_stats.txt
```bash
for i in {1..29}; do cp ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/chr${i}/mywagenome_chr${i}/chr${i}_SIFTDB_stats.txt ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/MYWA_SIFT_DATABASE; done

cp ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/chr1a/mywagenome_chr1a/chr1a_SIFTDB_stats.txt ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/MYWA_SIFT_DATABASE

cp ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/chr4a/mywagenome_chr4a/chr4a_SIFTDB_stats.txt ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/MYWA_SIFT_DATABASE
```

## Renaming Files
If your database files start with a 'chr', you must rename them without the 'chr' for the annotator to work.
_Example:_  `mv chr19.gz -> 19.gz; mv chr19.regions 19.regions; mv chr19_SIFTDB_stats.txt 19_SIFTDB_stats.txt`

```bash
cd ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/MYWA_SIFT_DATABASE

for i in {1..29}; do mv chr${i}.gz ${i}.gz; mv chr${i}.regions ${i}.regions; mv chr${i}_SIFTDB_stats.txt ${i}_SIFTDB_stats.txt; done

mv chr4a.gz 4a.gz
mv chr4a.regions 4a.regions
mv chr4a_SIFTDB_stats.txt 4a_SIFTDB_stats.txt

mv chr1a.gz 1a.gz
mv chr1a.regions 1a.regions
mv chr1a_SIFTDB_stats.txt 1a_SIFTDB_stats.txt
```
