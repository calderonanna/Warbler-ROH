
# 3. Centromere file
Centromeres position for non-model organisms, such as warblers do not yet exist. To get around this, we create a dummy centromere file with zeros and ones. I only did this once because all Setophaga have the same number of chromosomes...............(I think👀).

```bash
bcftools query -f '%CHROM\n' citrina_gtGQ.vcf.gz | uniq > chr.txt
awk '$1=$1" 0 1"' chr.txt > dummy_centro.txt
rm chr.txt
```
