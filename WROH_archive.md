# Archiving Data from WROH

## Move bam/bai files
```bash
#Set Variables
ids="/storage/home/abc6435/SzpiechLab/abc6435/Scripts/warbler_id_list.txt"
WROH_folder="/storage/home/abc6435/SzpiechLab/abc6435/WarblerROH"

if [ ! -d "$data_folder/bam" ]; then
    mkdir -p "$data_folder/bam"
fi

for i in `cat $ids`; do
    mv $WROH_folder/${i}/${i}*.bam $WROH_folder/data/bam
done

for i in `cat $ids`; do
    mv $WROH_folder/${i}/${i}*.bai $WROH_folder/data/bam
done
```

## Move sam files
```bash
#Set Variables
ids="/storage/home/abc6435/SzpiechLab/abc6435/Scripts/warbler_id_list.txt"
WROH_folder="/storage/home/abc6435/SzpiechLab/abc6435/WarblerROH"

if [ ! -d "$data_folder/sam" ]; then
    mkdir -p "$data_folder/sam"
fi

for i in `cat $ids`; do
    mv $WROH_folder/${i}/${i}*.sam $WROH_folder/data/sam
done
```

## Move trimmed files
```bash
#Set Variables
ids="/storage/home/abc6435/SzpiechLab/abc6435/Scripts/warbler_id_list.txt"
WROH_folder="/storage/home/abc6435/SzpiechLab/abc6435/WarblerROH"
data_folder="/storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/data"

if [ ! -d "$data_folder/trimmed" ]; then
    mkdir -p "$data_folder/trimmed"
fi

for i in `cat $ids`; do
    mv $WROH_folder/${i}/*trimmed*.gz $data_folder/trimmed
done
```

## Move vcf files
```bash
#Set Variables
ids="/storage/home/abc6435/SzpiechLab/abc6435/Scripts/warbler_id_list.txt"
WROH_folder="/storage/home/abc6435/SzpiechLab/abc6435/WarblerROH"
data_folder="/storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/data"

if [ ! -d "$data_folder/vcf" ]; then
    mkdir -p "$data_folder/vcf"
fi

for i in `cat $ids`; do
    mv $WROH_folder/${i}/*vcf* $data_folder/vcf
done
```

## Move fastq files
```bash
#Set Variables
ids="/storage/home/abc6435/SzpiechLab/abc6435/Scripts/warbler_id_list.txt"
WROH_folder="/storage/home/abc6435/SzpiechLab/abc6435/WarblerROH"
data_folder="/storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/data"

if [ ! -d "$data_folder/fastq" ]; then
    mkdir -p "$data_folder/fastq"
fi

for i in `cat $ids`; do
    mv $WROH_folder/${i}/fastq/*fastq* $data_folder/fastq
done
```

## Remove remaining folders and anything inside
```bash
for i in `cat $ids`; do
    rm -rf $WROH_folder/${i}/ 
done
```

## Archive WROH
```bash
nano /storage/home/abc6435/SzpiechLab/abc6435/archive_WROH.bash
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --mem=50GB
#SBATCH --time=48:00:00
#SBATCH --account=zps5164_sc

tar -czvf /storage/home/abc6435/SzpiechLab/abc6435/WROH.tar.gz /storage/home/abc6435/SzpiechLab/abc6435/WROH
```