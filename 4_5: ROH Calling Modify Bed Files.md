# Modify Bed Files Class A
Class A has a range of 0-1mb roh. Anything less than 0.5mb ROH is not reliable and could include a bunch of false positives. So, we want to subset the bed files by only including ROH>0.5mb. We will do this using awk

```bash
qsub -A zps5164_a_g_hc_default -l feature=rhel7 -l walltime=10:00:00 -l nodes=1:ppn=1 -l mem=100gb -I

cd ~/SzpiechLab/abc6435/WarblerROH/garlic/

#if column 5 has a value above 500000 OR if the line contains the word "track", print the line
cat kirtlandii_garlic.roh.bed  | awk -F '\t' '$5>500000 || /track/ {print$0}' > kirtlandii.roh.bed

cat citrina_garlic.roh.bed  | awk -F '\t' '$5>500000 || /track/ {print$0}' > citrina.roh.bed

cat ruticilla_garlic.roh.bed  | awk -F '\t' '$5>500000 || /track/ {print$0}' > ruticilla.roh.bed

# For fun, print the line with the longest ROH length; note, i did this a really stupid way because i didn't know how to get the max value in awk of column 5 (ROH lengths). So, I just played around with the '$5 >18000000` parameter to try to hone in on the largest segment. 
cd ~/SzpiechLab/abc6435/WarblerROH/garlic/
cat kirtlandii.roh.bed | awk -F '\t' '$5>18000000 || /track/ {print$0}'


```
