# SIFT 4G Database: File Organization 
We will make a parent_directory for each chromosome. Then each of the chromosome parent_directory should have a chr-src and gene-annotations-src folder. Your data should be organized like this. 
- 🗂parent_directory/
	- 🗂chr-src/
			-  contains unzipped fasta files
	- 🗂gene-annotations-src/
			- contains zipped gtf files
### Make Necessary Folders
```bash
#Make parent directory for each chromosome
cd ~/SzpiechLab/abc6435/WarblerROH/SIFT4G
for i in {1..29}; do mkdir chr${i}; done 
mkdir chr1a
mkdir chr4a
rm -R chr16/ #removing chr16 because MYWA genome has none. 

#Populate each parent directory with a chr-src folder
for i in {1..15}; do mkdir chr${i}/chr-src; done
for i in {17..29}; do mkdir chr${i}/chr-src; done
mkdir chr1a/chr-src
mkdir chr4a/chr-src

#Populate each parent directory with a gene-annotations-src folder
for i in {1..15}; do mkdir chr${i}/gene-annotation-src; done
for i in {17..29}; do mkdir chr${i}/gene-annotation-src; done
mkdir chr1a/gene-annotation-src
mkdir chr4a/gene-annotation-src
```


### Copy  spliced .fa files
```bash
cd /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/SIFT4G
for i in {1..15}; do cp GenomeSplit/*chr${i}.fa chr${i}/chr-src; done
for i in {17..29}; do cp GenomeSplit/*chr${i}.fa chr${i}/chr-src; done
cp GenomeSplit/*chr1a.fa chr1a/chr-src
cp GenomeSplit/*chr4a.fa chr4a/chr-src

 mv chr${i}/*.gtf chr${i}/gene-annotation-src; done
```
### Copy spliced .gtf files  
```bash
cd /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/SIFT4G
for i in {1..29}; do cp AnnotationsSplit/*chr${i}.gtf chr${i}/gene-annotations-src; done
cp AnnotationsSplit/*chr1a.gtf chr1a/gene-annotations-src
cp AnnotationsSplit/*chr4a.gtf chr4a/gene-annotations-src
```
