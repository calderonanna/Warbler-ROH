
# 5. Create an ROH Summary

A summary of ROH distribution can be obtained using the python file _calculate_ROH_fractions.pl_, from the  _.roh.bed_  file and a file with the individual id of each population.

## Create an ID file for each species
```bash
nano citrina_ID.txt
----------------------------------------------NANO-------------------------------------------------------
262 citrina  
2871 citrina 
TE22T01 citrina
TE30T02 citrina
SE25T02 citrina
----------------------------------------------END OF FILE ------------------------------------------------

nano kirtlandii_ID.txt
----------------------------------------------NANO-------------------------------------------------------
183195332 kirtlandii 
183194861 kirtlandii 
183195321 kirtlandii 
183195304 kirtlandii  
183194841 kirtlandii
183195326 kirtlandii
183195312 kirtlandii
----------------------------------------------END OF FILE ------------------------------------------------

nano ruticilla_ID.txt
----------------------------------------------NANO-------------------------------------------------------
163 ruticilla
1049 ruticilla
1940 ruticilla
4056 ruticilla
284029323 ruticilla
TF19T04 ruticilla
TF03T03 ruticilla
----------------------------------------------END OF FILE ------------------------------------------------
```

## Run Statistics Script With 6 Thresholds
`./calculate_ROH_fractions.pl input.roh.bed nb_class pop_ID.txt > output_file.txt`
```bash
qsub -A zps5164_a_g_hc_default -l feature=rhel7 -l walltime=10:00:00 -l nodes=1:ppn=1 -l mem=100gb -I

cd /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/garlic

#citrina
./calculate_ROH_fractions.pl citrina.roh.bed 6 citrina_ID.txt > citrina_roh_sum.txt

#ruticilla
./calculate_ROH_fractions.pl ruticilla.roh.bed 6 ruticilla_ID.txt > ruticilla_roh_sum.txt

#kirtlandii
./calculate_ROH_fractions.pl kirtlandii.roh.bed 6 kirtlandii_ID.txt > kirtlandii_roh_sum.txt

```

## Concatenate all species roh_sum 
First concatenate all into one text file then use nano to fixe the column header and remove the extra headers. Lastly, download it to your local machine. 
```bash
cat *roh_sum* > ROH_SUM.txt
nano ROH_SUM.txt
```

 

