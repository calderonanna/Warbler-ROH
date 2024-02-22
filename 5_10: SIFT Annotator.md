
# SIFT4G Annotations
Now that we have our database built and compiled, we can now run the sift annotator tool to generate predictions. I have been working in Linux, so use this link to find the instructions:  https://sift.bii.a-star.edu.sg/sift4g/Commandline.html. 

In all, it took about 2 hours to run. When the program is annotating, it will create temporary files for each chromosome. At the very end, it will merge all of those predictions and you should end up with two files: `.xls` and `.vcf`. For reference, my predictions vcf file is 36G so it will be big. 

## Download the Jave Executable File
```bash
qsub -A zps5164_a_g_hc_default -l feature=rhel7 -l walltime=10:00:00 -l nodes=1:ppn=1 -l mem=100gb -I

cd ~/SzpiechLab/abc6435/WarblerROH/SIFT4G
mkdir SIFT4G_Annotator
cd SIFT4G_Annotator
wget https://github.com/pauline-ng/SIFT4G_Annotator/raw/master/SIFT4G_Annotator.jar
```

## Make Results Folder 
```bash
cd ~/SzpiechLab/abc6435/WarblerROH/SIFT4G
mkdir SIFT_Predictions
cd SIFT_Predictions
```
## Annotate Variants
To run the SIFT 4G Annotator on Linux or Mac via command line, type the following command into the terminal:
`java -jar Path_to_SIFT4G_Annotator -c -i Path_to_input_vcf -d Path_to_SIFT4G_database directory -r Path_to your_results_folder -t`

**-c** : To run on command line
**-i** : Path to your input variants file in VCF format
**-d** : Path to SIFT database directory
**-r** : Path to your output results folder
**-t** : To extract annotations for multiple transcripts (Optional)

Path to SIFT4G_Annotator = `/storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/SIFT4G/SIFT4G_Annotator/SIFT4G_Annotator.jar`

Path to Input VCF (UNZIPPED) = `/storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/vcf/Setophaga/setophaga_filtered_isec_nomono.vcf`
Note: The vcf you will use is one that contains all of your samples. Make sure that it is a good quality vcf with the filtering already completed. 
 
Path to SIFT4G Database directory = `/storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/SIFT4G/MYWA_SIFT_DATABASE`

Path to Results Folder =
`/storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/SIFT4G/SIFT_Predictions`

```bash
java -jar /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/SIFT4G/SIFT4G_Annotator/SIFT4G_Annotator.jar -c -i /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/vcf/Setophaga/setophaga_filtered_isec_nomono.vcf -d /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/SIFT4G/MYWA_SIFT_DATABASE -r /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/SIFT4G/SIFT_Predictions -t
```
