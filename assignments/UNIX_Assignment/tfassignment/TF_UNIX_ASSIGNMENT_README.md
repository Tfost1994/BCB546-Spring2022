#UNIX Assignment

##Data Inspection

###Attributes of `fang_et_al_genotypes`

```
head -n2 fang_et_al_genotypes.txt

wc fang_et_al_genotypes.txt

du -h fang_et_al_genotypes.txt

awk -F "\t" '{print NF; exit}' fang_et_al_genotypes.txt

tail -n +6 fang_et_al_genotypes.txt | awk -F "\t" '{print NF; exit}'

grep -v "^SNP" fang_et_al_genotypes.txt | cut -f3 | sort | uniq -c

```

By inspecting this file I learned that:

1. I first started by using the head command to get an idea of what was going on with the dataset.
2. Provide the number of lines, words and characters of the SNP file. There are 2783 lines, 2744038 words and 11051939 characters. This file is much larger than the SNP file. 
3. Inspecting the file size using the du command, which is human readable. The file size for the fang genotypes file is 6.5M, confirming that this file is larger, actually much larger, than the SNP file.
4. Using awk to count the number of columns. As we do this, we find there are 986 columns. Seems like a large number, especially when we compare to the SNP file below.
5. I then wanted to replicate what I did in step 4 using the tail command. The output I received was identical to that of step 4 - 986 columns. Good!
6. I see in the later part of the project, we are interested in Maize/Teosinte information, which is provided to us in the directions. Therefore, I wanted to inspect column 3 alittle closer and to see if my numbers for each filtering process match, and they do! I was concerned when 975 teosinte lines + 1573 maize lines did not equal the total 2783 but it makes sense with the code run as there are other groups such as "TRIPS, ZMHUE, ZDIPL, etc..".

###Attributes of `snp_position.txt`

```
head -n5 snp_position.txt

wc snp_position.txt

du -h snp_position.txt

awk -F "\t" '{print NF; exit}' snp_position.txt

tail -n +6 snp_position.txt | awk -F "\t" '{print NF; exit}'
```

By inspecting this file I learned that:

1. I wanted to get a good idea of what the SNP data looks like.
2. Provide the number of lines, words and characters of the SNP file. There are 984 lines, 13198 words and 82763 characters.
3. Inspecting the file size using the du command, which is human readable. The file size for the SNP file is 41K.
4. Using awk to count the number of columns. There are only 15 columns for this dataset.
5. I then wanted to replicate what I did in step 4 using the tail command. The output I received was identical to that of step 4 - 15 columns. Good!

##Data Processing

###SNP Data

```
grep -c "ZMM.*" fang_et_al_genotypes.txt
```

1. we found from our data inspection that there are two lines that act as headers for this dataset. Raw data does not start until line 3. Let's remove the header before sorting that needs to be done for 

###Maize Data

```
grep -c "ZMM.*" fang_et_al_genotypes.txt
```

1. I want to grab all of the data that is associated with maize and get a count of the number of lines that are associated. I used the .* as a wildcard, otherwise I couldve used: grep -E "ZMMIL|ZMMMR|ZMMLR" fang_et_al_genotypes.txt | wc ; which also would give me the same answer - 1573 lines. Using the wildcard is more robust and good practice.



###Teosinte Data

```
grep -c "ZMP.*" fang_et_al_genotypes.txt
```

1. I want to grab all of the data that is associated with the teosinte data and get a count of the number of lines that are associated. I used the .* as a wildcard, otherwise I couldve used: grep -E "ZMPJA|ZMPBA|ZMPIL" fang_et_al_genotypes.txt | wc
 ; which also would give me the same answer - 975 lines.