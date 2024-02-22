# 4. Running garlic
GARLIC will generate 5 files:  *.110SNPs.kde, *.error, *.freq.gz, *.log, *.roh.bed

`garlic --auto-winsize --auto-overlap-frac --winsize 100 --centromere centromeres_file.txt --size-bounds 1000000 2000000 3000000 4000000 5000000 --tped tped_file.tped --tfam tfam_file --tgls tgls_file.tgls --gl-type GQ --resample 40 --out roh_calling_file`

-   `--auto-winsize`  If --weighted flag is set, it guesses the best window size based on SNP density. Otherwise, it initiates 
    an ad hoc method for authomatically selecting the number of SNPs in which to calculated LOD scores. It starts at the value
    specified by --winsize flag and increasy by - SNPs until its finished. 
-   `--auto-overlap-frac`  The minimum fraction of overlapping windows above the LOD cutoff required  
    to begin constructing a run. ROH will have a lower bound size threshold of winsize*overlap-frac.  
    If set to 0, GARLIC sets the value to the lowest sensible value: 1/winsize. Default: false
-   `--auto-winsize-step <int>`  Step size for automatic window selection algorithm. Default: 10.25
-   `--winsize <int>`  The window size in # of SNPs in which to calculate LOD scores. Default: 10
-   `--centromere`  Provide custom centromere boundaries. Format -chr- -start- -end-. Default: none  
    OR use  `--build--`  to use human centromeres position
-   `--tped`  path to tped file
-   `--tfam`  path to tfam file
-   `--tgls`  path to tgls filewinsize-multi … ` Provide several window sizes (in # of SNPs) to calculate LOD scores. LOD score
	 KDEs for each window size will be output for inspection.  Default: -1
-   `--error <double>`  The assumed genotyping error rate. Default: -1
-   `--gl-type <string>`  Specify the form of the genotype likelihood data: GQ, GL, or PL
-   `--resample`  Number of resamples for estimating allele frequencies (between 40 and 100)
-   `--out`  path and suffix to the ouput files
- `--nclust <int>` Set number of clusters for GMM classification of ROH lengths.
	Default: 3
	
**NOTE ABOUT SIZE BOUNDS** 

Defining the size bounds as 1000000 2000000 3000000 4000000 5000000, GARLIC will produce 6 categories (A,B,C,D,E,F)

<---1MB---2MB---3MB---4MB---5MB--->

___A__|__B__|__C__|__D__|__E__|__F___

## ROH Thresholds Fixed
```bash
qsub -A zps5164_a_g_sc_default -l feature=rhel7 -l walltime=5:00:00 -l nodes=1:ppn=1 -l mem=100gb -I

cd ~/SzpiechLab/abc6435/WarblerROH/tgls_tped_tfam
#citrina
garlic --auto-winsize --auto-overlap-frac --winsize 100 --tped citrina.tped --tfam citrina_mod.tfam --tgls citrina.tgls --gl-type GQ --resample 40 --centromere dummy_centro.txt --size-bounds 1000000 2000000 3000000 4000000 5000000 --out ~/SzpiechLab/abc6435/WarblerROH/garlic/citrina_garlic

#ruticilla
garlic --auto-winsize --auto-overlap-frac --winsize 100 --tped ruticilla.tped --tfam ruticilla_mod.tfam --tgls ruticilla.tgls --gl-type GQ --resample 40 --centromere dummy_centro.txt --size-bounds 1000000 2000000 3000000 4000000 5000000 --out ~/SzpiechLab/abc6435/WarblerROH/garlic/ruticilla_garlic


#kirtlandii
garlic --auto-winsize --auto-overlap-frac --winsize 100 --tped kirtlandii.tped --tfam kirtlandii_mod.tfam --tgls kirtlandii.tgls --gl-type GQ --resample 40 --centromere dummy_centro.txt --size-bounds 1000000 2000000 3000000 4000000 5000000 --out ~/SzpiechLab/abc6435/WarblerROH/garlic/kirtlandii_garlic


------------------------------------------------END OF SCRIPT---------------------------------------------
qsub garlic.bash
```


