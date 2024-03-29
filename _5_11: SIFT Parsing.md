# SIFT4G Parsing 

## Make a Directory for Filtered Sites. 
```bash
cd ~/SzpiechLab/abc6435/WarblerROH/SIFT4G
mkdir SIFT_Filtering
```
## Modifying Predictions File 
```bash
# Remove the header and filter out non-annotated sites
grep -v "^##" /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/SIFT4G/SIFT_Predictions/setophaga_filtered_isec_nomono_SIFTpredictions.vcf | grep "SIFTINFO=" > /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/SIFT4G/SIFT_Filtering/SIFT_Setophaga_Annotated_Sites.txt
```

## Subsetting the Real File
We only want to keep the following columns: Chrom, Position, Alt,  Ref, and Column 8 (1,2,4,5,8). Because our file is vcf-like and not an actual vcf, we will do this using AWK. 

```bash
#Print only columns 1,2,4,5, and 8
awk '{print $1, $2, $4, $5, $8}' SIFT_Setophaga_Annotated_Sites.txt > SIFT_Set_cols_subset.txt

#Add pipes in between the columns
awk '{print $1 "|" $2 "|" $3 "|" $4 "|" $5}' SIFT_Set_cols_subset.txt > SIFT_Set_cols_subset_piped.txt

#Remove the pipes and instead delimit the file with tabs. Also only include the following columns: 1,2,3,4,10,17, 22, 29, 34, 41, 46, and 53. 
awk -F"[|]" '{print $1 "\t" $2 "\t" $3 "\t" $4 "\t" $10 "\t" $17 "\t" $22 "\t" $29 "\t" $34 "\t" $41 "\t" $46 "\t" $53}' SIFT_Set_cols_subset_piped.txt > SIFT_Set_cols_subset_piped_effects.txt

#Remove this regular expression
sed -e "s/\r//g" SIFT_Set_cols_subset_piped_effects.txt > SIFT_Set_Clean.txt

```

## Download Cleaned SIFT File
Now that the SIFT File has been cleaned, you can do the data wrangling in R. 
