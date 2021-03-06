# Remove missings
- **Remove variants and samples which have missing genotypes with 0.10 to 0.01 frequencies.**

> - Single processing
```
file1='GROUP'
file2='.option1'
file3='.op1'
file4='.cmd'

path0=/${file1}/data/
path1=${path0}test/
path2=${path0}inform/
path3=${path0}cmd_list/

# Remove variants and samples which have missing genotypes with 0.10 to 0.01 frequencies.
for i in {1..22} X Y;do plink --bfile ${path0}${file1}chr${i}${file2} --geno 0.1 --make-bed --out ${path1}${file1}chr${i}${file3}.snp90;done
for i in {1..22} X Y;do plink --bfile ${path1}${file1}chr${i}${file3}.snp90 --mind 0.1 --make-bed --out ${path1}${file1}chr${i}${file3}.smp90;done

for j in {9..1}
do
    a=$(echo "scale = 2; $j / 100" | bc);b=$(echo "scale = 2; 99 - $j" | bc);c=$(echo "scale = 2; 100 - $j" | bc)
    for i in {1..22} X Y;do plink --bfile ${path1}${file1}chr${i}${file3}.smp$b --geno ${a} --make-bed --out ${path1}${file1}chr${i}${file3}.snp${c};done
    for i in {1..22} X Y;do plink --bfile ${path1}${file1}chr${i}${file3}.snp$b --mind ${a} --make-bed --out ${path1}${file1}chr${i}${file3}.smp${c};done
done

# Check the number of variants and samples.
for j in {90..99}
do
    for i in {1..22} X Y;do wc -l ${path1}${file1}chr${i}${file3}.snp${j}.bim;done > ${path2}${file1}${file3}.snp${j}.inf0
    awk '{print $1}' ${path2}${file1}${file3}.snp${j}.inf0 > ${path2}${file1}${file3}.snp${j}.inf
    for i in {1..22} X Y;do wc -l ${path1}${file1}chr${i}${file3}.smp${j}.fam;done > ${path2}${file1}${file3}.smp${j}.inf0
    awk '{print $1}' ${path2}${file1}${file3}.smp${j}.inf0 > ${path2}${file1}${file3}.smp${j}.inf
done

for i in {1..22} X Y;do wc -l ${file1}chr${i}${file2}.bim;done > ${path2}${file1}${file3}.snp.inf0
awk '{print $1}' ${path2}${file1}${file3}.snp.inf0 > ${path2}${file1}${file3}.snp.inf
for i in {1..22} X Y;do wc -l ${file1}chr${i}${file2}.fam;done > ${path2}${file1}${file3}.smp.inf0
awk '{print $1}' ${path2}${file1}${file3}.smp.inf0 > ${path2}${file1}${file3}.smp.inf

rm ${path2}${file1}${file3}.*.inf0

# Generate chromosome numbers' index
for i in {1..22} X Y;do echo ${i};done > ${path2}${file1}${file3}.idx

a=$(for j in {90..99} X Y;do printf ${path2}${file1}${file3}.snp${j}.inf" ";done)
paste ${path2}${file1}${file3}.idx ${path2}${file1}${file3}.snp.inf $a > ${path2}${file1}${file3}.snp_90_99.inf
a=$(for j in {90..99} X Y;do printf ${path2}${file1}${file3}.smp${j}.inf" ";done)
paste ${path2}${file1}${file3}.idx ${path2}${file1}${file3}.smp.inf $a > ${path2}${file1}${file3}.smp_90_99.inf
```

> - Multi-processing
```
$ emacs cmd.sh
#!/bin/bash

file1='GROUP'
file2='.option1'
file3='.op1'
file4='.cmd'

path0=/${file1}/data/
path1=${path0}test/
path2=${path0}inform/
path3=${path0}cmd_list/


# Remove variants and samples which have missing genotypes with 0.10 to 0.01 frequencies.
for i in {1..22} X Y;do echo "plink --bfile ${path0}${file1}_chr${i}${file2} --geno 0.1 --make-bed --out ${path1}${file1}_chr${i}${file3}.snp90";done > ${path3}${file1}${file3}.snp90.cmd
for i in {1..22} X Y;do echo "plink --bfile ${path1}${file1}_chr${i}${file3}.snp90 --mind 0.1 --make-bed --out ${path1}${file1}_chr${i}${file3}.smp90";done > ${path3}${file1}${file3}.smp90.cmd

for j in {9..1}
do
    a=$(echo "scale = 2; $j / 100" | bc);b=$(echo "scale = 2; 99 - $j" | bc);c=$(echo "scale = 2; 100 - $j" | bc)
    for i in {1..22} X Y;do echo "plink --bfile ${path1}${file1}_chr${i}${file3}.smp$b --geno ${a} --make-bed --out ${path1}${file1}_chr${i}${file3}.snp${c}";done > ${path3}${file1}${file3}.snp${c}.cmd
    for i in {1..22} X Y;do echo "plink --bfile ${path1}${file1}_chr${i}${file3}.snp$b --mind ${a} --make-bed --out ${path1}${file1}_chr${i}${file3}.smp${c}";done > ${path3}${file1}${file3}.smp${c}.cmd
done

for j in {90..99}
do
    parallel {} < ${path3}${file1}${file3}.snp${j}.cmd && parallel {} < ${path3}${file1}${file3}.smp${j}.cmd
done

# Check number of variants and samples from 0.90 to 0.95 frequencies.
for j in {90..99}
do
    for i in {1..22} X Y;do wc -l ${path1}${file1}_chr${i}${file3}.snp${j}.bim;done > ${path2}${file1}${file3}.snp${j}.inf0
    awk '{print $1}' ${path2}${file1}${file3}.snp${j}.inf0 > ${path2}${file1}${file3}.snp${j}.inf
    for i in {1..22} X Y;do wc -l ${path1}${file1}_chr${i}${file3}.smp${j}.fam;done > ${path2}${file1}${file3}.smp${j}.inf0
    awk '{print $1}' ${path2}${file1}${file3}.smp${j}.inf0 > ${path2}${file1}${file3}.smp${j}.inf
done

for i in {1..22} X Y;do wc -l ${file1}_chr${i}${file2}.bim;done > ${path2}${file1}${file3}.snp.inf0
awk '{print $1}' ${path2}${file1}${file3}.snp.inf0 > ${path2}${file1}${file3}.snp.inf
for i in {1..22} X Y;do wc -l ${file1}_chr${i}${file2}.fam;done > ${path2}${file1}${file3}.smp.inf0
awk '{print $1}' ${path2}${file1}${file3}.smp.inf0 > ${path2}${file1}${file3}.smp.inf

rm ${path2}${file1}${file3}.*.inf0

# Generate chromosome numbers' index
for i in {1..22} X Y;do echo ${i};done > ${path2}${file1}${file3}.idx

a=$(for j in {90..99} X Y;do printf ${path2}${file1}${file3}.snp${j}.inf" ";done)
paste ${path2}${file1}${file3}.idx ${path2}${file1}${file3}.snp.inf $a > ${path2}${file1}${file3}.snp_90_99.inf
a=$(for j in {90..99} X Y;do printf ${path2}${file1}${file3}.smp${j}.inf" ";done)
paste ${path2}${file1}${file3}.idx ${path2}${file1}${file3}.smp.inf $a > ${path2}${file1}${file3}.smp_90_99.inf
```
