# SIFT 4G Database: Configurations
A configuration file is needed to specify the path to the parent directory, the protein database and sift4g program. In addition to making your configuration files for each chromosome parent directory, you will also need to retrieve and save a copy of the uniref90.fasta. 
UniProt website: (https://www.uniprot.org/help/downloads)
More about UniRef: https://academic.oup.com/bioinformatics/article/23/10/1282/197795

## Obtain uniref90 

```bash
qsub -A zps5164_a_g_hc_default -l feature=rhel7 -l walltime=10:00:00 -l nodes=1:ppn=1 -l mem=100gb -I 

cd ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/
mkdir ProteinDB
cd ProteinDB

wget -c https://ftp.uniprot.org/pub/databases/uniprot/uniref/uniref90/uniref90.fasta.gz
bgzip -d uniref90.fasta.gz
```

## Create Configuration Files: 
Because we are splitting everything by chromosome, we will need to create a configuration file for each of our chromosomes. We will do this using a loop. 

### Configuration files for Chr1-29
```bash
for i in {1..29}; do echo "
GENETIC_CODE_TABLE=1
GENETIC_CODE_TABLENAME=Standard
MITO_GENETIC_CODE_TABLE=2
MITO_GENETIC_CODE_TABLENAME=Vertebrate Mitochondrial

PARENT_DIR=/storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/SIFT4G/chr${i}
ORG=setophaga_coronata
ORG_VERSION=mywagenome_chr${i}

#Running SIFT 4G
SIFT4G_PATH=~/SzpiechLab/abc6435/WarblerROH/SIFT4G/BuildSIFT/sift4g/bin/sift4g
PROTEIN_DB=/storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/SIFT4G/ProteinDB/uniref90.fasta

# Sub-directories, don't need to change
GENE_DOWNLOAD_DEST=gene-annotation-src
CHR_DOWNLOAD_DEST=chr-src
LOGFILE=Log.txt
ZLOGFILE=Log2.txt
FASTA_DIR=fasta
SUBST_DIR=subst
ALIGN_DIR=SIFT_alignments
SIFT_SCORE_DIR=SIFT_predictions
SINGLE_REC_BY_CHR_DIR=singleRecords
SINGLE_REC_WITH_SIFTSCORE_DIR=singleRecords_with_scores
DBSNP_DIR=dbSNP

# Doesn't need to change
FASTA_LOG=fasta.log
INVALID_LOG=invalid.log
PEPTIDE_LOG=peptide.log
ENS_PATTERN=ENS
SINGLE_RECORD_PATTERN=:change:_aa1valid_dbsnp.singleRecord" > /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/SIFT4G/chr${i}/mywa_config_${i}.txt; done
```

### Configuration file for Chr1a 
```bash
cd ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/chr1a
nano mywa_config_1a.txt
-------------------------------------------NANO-----------------------------------------------
GENETIC_CODE_TABLE=1
GENETIC_CODE_TABLENAME=Standard
MITO_GENETIC_CODE_TABLE=2
MITO_GENETIC_CODE_TABLENAME=Vertebrate Mitochondrial

PARENT_DIR=/storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/SIFT4G/chr1a
ORG=setophaga_coronata
ORG_VERSION=mywagenome_chr1a

#Running SIFT 4G
SIFT4G_PATH=~/SzpiechLab/abc6435/WarblerROH/SIFT4G/BuildSIFT/sift4g/bin/sift4g
PROTEIN_DB=/storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/SIFT4G/ProteinDB/uniref90.fasta

# Sub-directories, don't need to change
GENE_DOWNLOAD_DEST=gene-annotation-src
CHR_DOWNLOAD_DEST=chr-src
LOGFILE=Log.txt
ZLOGFILE=Log2.txt
FASTA_DIR=fasta
SUBST_DIR=subst
ALIGN_DIR=SIFT_alignments
SIFT_SCORE_DIR=SIFT_predictions
SINGLE_REC_BY_CHR_DIR=singleRecords
SINGLE_REC_WITH_SIFTSCORE_DIR=singleRecords_with_scores
DBSNP_DIR=dbSNP

# Doesn't need to change
FASTA_LOG=fasta.log
INVALID_LOG=invalid.log
PEPTIDE_LOG=peptide.log
ENS_PATTERN=ENS
SINGLE_RECORD_PATTERN=:change:_aa1valid_dbsnp.singleRecord
------------------------------------END OF FILE-----------------------------------------------
```

### Configuration file for Chr4a 
```bash
cd ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/chr4a
nano mywa_config_4a.txt
-------------------------------------------NANO-----------------------------------------------
GENETIC_CODE_TABLE=1
GENETIC_CODE_TABLENAME=Standard
MITO_GENETIC_CODE_TABLE=2
MITO_GENETIC_CODE_TABLENAME=Vertebrate Mitochondrial

PARENT_DIR=/storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/SIFT4G/chr4a
ORG=setophaga_coronata
ORG_VERSION=mywagenome_chr4a

#Running SIFT 4G
SIFT4G_PATH=~/SzpiechLab/abc6435/WarblerROH/SIFT4G/BuildSIFT/sift4g/bin/sift4g
PROTEIN_DB=/storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/SIFT4G/ProteinDB/uniref90.fasta

# Sub-directories, don't need to change
GENE_DOWNLOAD_DEST=gene-annotation-src
CHR_DOWNLOAD_DEST=chr-src
LOGFILE=Log.txt
ZLOGFILE=Log2.txt
FASTA_DIR=fasta
SUBST_DIR=subst
ALIGN_DIR=SIFT_alignments
SIFT_SCORE_DIR=SIFT_predictions
SINGLE_REC_BY_CHR_DIR=singleRecords
SINGLE_REC_WITH_SIFTSCORE_DIR=singleRecords_with_scores
DBSNP_DIR=dbSNP

# Doesn't need to change
FASTA_LOG=fasta.log
INVALID_LOG=invalid.log
PEPTIDE_LOG=peptide.log
ENS_PATTERN=ENS
SINGLE_RECORD_PATTERN=:change:_aa1valid_dbsnp.singleRecord
------------------------------------END OF FILE-----------------------------------------------
```

