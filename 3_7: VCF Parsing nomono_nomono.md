# Transform VCF into CSV
Using bcftools only include the following columns: CHROM, POS, REF, ALT, and GENOTYPES. Then remove the header (not sure if bcftools does that for you, but if not then use grep -v "^##". Will submit it as a script because the file is quite large. 

## Subset Columns 
Print only columns 1,2,4,5, and 10-28, which correspond to CHROM, POS, REF, ALT, SAMPLE COLUMNS 10-28
```bash
salloc -N 1 -n 1 --mem-per-cpu=100GB -t 5:00:00

cd /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/vcf/species

bcftools query -H -f '%CHROM %POS  %REF  %ALT [ %GT]\n' citrina_filtered_isec_nomono_nomono.vcf.gz >> citrina_filtered_isec_nomono_nomono.txt

bcftools query -H -f '%CHROM %POS  %REF  %ALT [ %GT]\n' kirtlandii_filtered_isec_nomono_nomono.vcf.gz >> kirtlandii_filtered_isec_nomono_nomono.txt

bcftools query -H -f '%CHROM %POS  %REF  %ALT [ %GT]\n' ruticilla_filtered_isec_nomono_nomono.vcf.gz >> ruticilla_filtered_isec_nomono_nomono.txt

```
## Convert TXT into CSV
```bash
#Convert to CSV
sed 's/ \+/,/g' citrina_filtered_isec_nomono_nomono.txt > citrina_filtered_isec_nomono_nomono.csv
sed 's/ \+/,/g' kirtlandii_filtered_isec_nomono_nomono.txt > kirtlandii_filtered_isec_nomono_nomono.csv
sed 's/ \+/,/g' ruticilla_filtered_isec_nomono_nomono.txt > ruticilla_filtered_isec_nomono_nomono.csv
```


