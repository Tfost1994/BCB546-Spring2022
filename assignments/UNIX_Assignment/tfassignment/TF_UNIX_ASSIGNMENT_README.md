#UNIX Assignment

#The final directories with associated files to be graded at the end of the assignment are found in the GRADEME directory! The rest of the directories and files were for me and my data processing.

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

1. I first started by using the head command to get an idea of what was going on with the dataset. There is a header line with column names but there are no header lines with "#" and the nucleotide data starts on line 2. The formatting of the dataset is very weird, and needs to be transposed later on.
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

1. I wanted to get a good idea of what the SNP data looks like. Column 1 is where our SNP_ID is, and this is the column where we will sort and join later on.
2. Provide the number of lines, words and characters of the SNP file. There are 984 lines, 13198 words and 82763 characters.
3. Inspecting the file size using the du command, which is human readable. The file size for the SNP file is 41K.
4. Using awk to count the number of columns. There are only 15 columns for this dataset.
5. I then wanted to replicate what I did in step 4 using the tail command. The output I received was identical to that of step 4 - 15 columns. Good!

##Data Processing

###SNP Data

```
cut -f 1,3,4 snp_position.txt > cut_snp.position.txt

sort -c -k1,1 cut_snp_position.txt

sort -k1,1 cut_snp_position.txt > sorted_snps.txt

mv snp_position.txt Sorted_data
```

1. I wanted to extract only those columns that we're interested in before making any sorts or joins. We really only need the SNP_IDs, Chromosome in which the SNP_ID is located on, and the Position of the SNP_ID on a specific Chromosome.
2. I wanted to check to see if there were any SNP_IDs (1st column) that were out of order.
3. Because there were SNP_IDs out of order, I created a new dataset that had them all in order and called it "sorted_snps.txt. Sorting based on the SNP_ID is needed for joining later on.
4. Both of the cut_snp.position.txt and the snp_position.txt files were moved to a "SNP_data" directory for organization

###Maize Data

```
grep -c "ZMM.*" fang_et_al_genotypes.txt

grep -E "ZMM.*|Group" fang_et_al_genotypes.txt > maize_genotypes.txt

awk -f transpose.awk maize_genotypes.txt > transposed_maize_genotypes.txt

head -n2 transposed_maize_genotypes.txt

sort -c -k1,1 transposed_maize_genotypes.txt

sort -k1,1 transposed_maize_genotypes.txt > sorted_maize.txt

sort -c -k1,1 sorted_maize.txt

wc sorted_maize.txt

join -1 1 -2 1 sorted_snps.txt sorted_maize.txt | head -n3

join -1 1 -2 1 sorted_snps.txt sorted_maize.txt > joined_maize.txt

mv maize_genotypes.txt Rawest_genotype_data
```

1. I want to grab all of the data that is associated with maize and get a count of the number of lines that are associated. I used the .* as a wildcard, otherwise I couldve used: grep -E "ZMMIL|ZMMMR|ZMMLR" fang_et_al_genotypes.txt | wc ; which also would give me the same answer - 1573 lines. Using the wildcard is more robust and good practice and its possible to do this because there are no other "ZMM" in this column that are not associated with maize - only ZMMLR, ZMMMR and ZMMIL.
2. Create a new txt file based upon the analysis from step 1, while also including "GROUP" which is part of the header line. Otherwise, we will not have a header for each column.
3. Because the formatting of the dataset is weird, we must transpose it using the script provided to us by Dr. Hufford. I utilized the script with filtering for maize genotypes before transposition.
4. I wanted to take a look at my new dataset before trying any sorting scripts. 
5. Now that I know that the ID's are in the first column, I want to check to see if the column is already in order.
5. Because the column was not in order, I must do that before attempting a join.
6. Checking to see if the file is sorted, and it is!
7. I wanted to count the number of lines, words, and characters to see if the file makes sense. We should expect the same number of rows (SNP IDs) between both datasets but the total number of words/characters should be larger for the maize dataset - which it is.
8. Before I create a new dataset from the join script, I wanted to see if it worked correctly. It looks like 1st column is the ID, 2nd is chromosome, 3rd is the position and the rest of the columns are nucleotide information. Looks promising!
9. Create our joined dataset. The join was made using the 1st column (SNP ID) for each of the datasets.
10. All of 'raw' data was sent to the "Rawest_genotype_data" directory. All of the "transposed" data was sent to the "transposed_data" directory. All of the "sorted" data was sent to the "Sorted_data" directory. This just helps keep things organized. The code will not work if copy and pasted unless in the correct directory but in a practical sense, once we run the code, we shouldnt have to run it again, unless we notice a mistake (this happened once and I had to move the files back a directory into tfassignment by using mv "MYfile" ../) as tfassignment was the main directory where I did most of the data processing.

#For a given chromosome:

```
awk '$2 ~ /^1$/' joined_maize.txt > chr1_maize.txt

sort -k3,3n chr1_maize.txt > Increasing_with_?_chr1_maize.txt
```
1. Utilizing awk to essentially identify the pattern of a specific chromosome in the 2nd field and create a new file with that information.
2. Sorting numerically (increasing) for the position on a given chromosome (column 3) and naming a new file. Missing values are already "?" so we dont have to change those at this point.

##NEXT

```
sort -k3,3nr chr1_maize.txt > d_chr1.txt

sed 's/?/-/g' d_chr1.txt > Decreasing_with_-_chr1_maize.txt

```

1. Sorting in decreasing order for position from the individual chromosome information that was created using awk above.
2. Changing the global missing values of "?" to "-".

###Finally

```
grep "multiple" joined_maize.txt > multiple_maize.txt
wc multiple_maize.txt
```

1. Searching and grabbing lines associated with "multiple" for the entire maize dataset.
2. Getting the number of lines, words and characters as a check to see if the script was effective - it was.

###Teosinte Data

```
grep -c "ZMP.*" fang_et_al_genotypes.txt

grep -E "ZMP.*|Group" fang_et_al_genotypes.txt > teosinte_genotypes.txt

awk -f transpose.awk teosinte_genotypes.txt > transposed_teosinte_genotypes.txt

head -n2 transposed_teosinte_genotypes.txt

sort -c -k1,1 transposed_teosinte_genotypes.txt

sort -k1,1 transposed_teosinte_genotypes.txt > sorted_teosinte.txt

sort -c -k1,1 sorted_teosinte.txt

wc sorted_teosinte.txt

join -1 1 -2 1 sorted_snps.txt sorted_teosinte.txt | head -n3

join -1 1 -2 1 sorted_snps.txt sorted_teosinte.txt > joined_teosinte.txt
```

1. I want to grab all of the data that is associated with the teosinte data and get a count of the number of lines that are associated. I used the .* as a wildcard, otherwise I couldve used: grep -E "ZMPJA|ZMPBA|ZMPIL" fang_et_al_genotypes.txt | wc - which also would give me the same answer - 975 lines. Using the wildcard is more robust and good practice and its possible to do this because there are no other "ZMP" in this column that are not associated with teosinte - only ZMPIL, ZMPJA and ZMPBA.
2. Create a new file with the desired 975 lines of teosinte information, while also including "GROUP" which is part of the header line. Otherwise, we will not have a header for each column.
3. Because the formatting of the dataset is weird, we must transpose it using the script provided to us by Dr. Hufford. I utilized the script with filtering for teosinte before transposition.
4. I wanted to take a look at my new dataset before trying any sorting scripts. 
5. Now that I know that the ID's are in the first column, I want to check to see if the column is already in order.
5. Because the column was not in order, I must do that before attempting a join.
6. Checking to see if the file is sorted, and it is!
7. I wanted to count the number of lines, words, and characters to see if the file makes sense. We should expect the same number of rows (SNP IDs) between both datasets but the total number of words/characters should be larger for the maize dataset - which it is.
8. Before I create a new dataset from the join script, I wanted to see if it worked correctly. It looks like 1st column is the ID, 2nd is chromosome, 3rd is the position and the rest of the columns are nucleotide information. Looks promising!
9. Create our joined dataset. The join was made using the 1st column (SNP ID) for each of the datasets.
10. All of 'raw' data was sent to the "Rawest_genotype_data" directory. All of the "transposed" data was sent to the "transposed_data" directory. All of the "sorted" data was sent to the "Sorted_data" directory. This just helps keep things organized. The code will not work if copy and pasted unless in the correct directory but in a practical sense, once we run the code, we shouldnt have to run it again, unless we notice a mistake (this happened once and I had to move the files back a directory into tfassignment by using mv "MYfile" ../) as tfassignment was the main directory where I did most of the data processing.

#For a given chromosome:

```
awk '$2 ~ /^1$/' joined_teosinte.txt > chr1_teosinte.txt

sort -k3,3n chr1_teosinte.txt > Increasing_with_?_chr1_teosinte.txt
```

1. Utilizing awk to essentially identify the pattern of a specific chromosome in the 2nd field and create a new file with that information.
2. Sorting numerically (increasing) for the position on a given chromosome (column 3) and naming a new file. Missing values are already "?" so we dont have to change those at this point.

##NEXT

```
sort -k3,3nr chr1_teosinte.txt > d_chr1.txt

sed 's/?/-/g' d_chr1.txt > Decreasing_with_-_chr1_teosinte.txt

```

1. Sorting in decreasing order for position from the individual chromosome information that was created using awk above.
2. Changing the global missing values of "?" to "-".

###Finally

```
grep "multiple" joined_teosinte.txt > multiple_teosinte.txt
wc multiple_teosinte.txt
```
1. Searching and grabbing lines associated with "multiple" for the entire teosinte dataset.
2. Getting the number of lines, words and characters as a check to see if the script was effective - it was.