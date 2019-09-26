#Martha Ibore_UNIX Assignment

##Data inspection

###Attributes of the fang_et_al_genotypes dataset


Using “head” to check the first 5 rows of the fang et al. dataset

```
$ head -n 5 fang_et_al_genotypes.txt
```

**Comment**: Running the above code listed a very elaborate output which was hard to interpret due to so many columns. So, only using "head" is not the best way to inspect our data. So we will use awk to determine the number of columns for the first five rows of this dataset.


Using "awk" to inspect the fang et al. dataset

```
$ head -n 5 fang_et_al_genotypes.txt | awk -F "\t" '{print NF; exit}'
```

**Comment:** Running the above code showed that the fang et al_genotypes dataset has 986 columns.


Because it is important for a dataset to have an equal number of columns at the beginning and the end of the of the dataset, the next step was to check the number of columns for the last 5 rows of the dataset using "tail".

Using "tail" to inspect the fang et al. dataset

```
$ tail -n 5 fang_et_al_genotypes.txt | awk -F "\t" '{print NF; exit}'
```

**Comment:** After using the tail command, it showed that there were 986 columns at the end of the file. Therefore, the dataset is good! Another question was, "how many rows does this dataset have?"

Using “wc” to check the first 5 rows of the fang et al. dataset

```
$ wc -l fang_et_al_genotypes.txt
```

**Answer:** fang_et_al_genotypes.txt has 2783 rows.


Another question is what the file size is. Using "du", this question was answered.

```
$ du -h fang_et_al_genotypes.txt
```

**Answer:** fang_et_al_genotypes.txt is 6.1M


Checking the fang et al dataset to determine if it has any non-ASCII characters

```
$ file fang_et_al_genotypes.txt 
``` 

**Comment:** fang_et_al_genotypes.txt had ASCII text, with very long lines. Therefore, this file does not have any hidden non-ASCII characters.


Answering the question if the fang_et_al dataset has headers by checking only columns 1-10.

```
head -5 fang_et_al_genotypes.txt | cut -f 1-10 | column -t
```

**Answer**: Yes, columns 1-10 have the headings listed here: 

Sample_ID  JG_OTU  Group  abph1.20  abph1.22  ae1.3  ae1.4 ae1.5  an1.4  ba1.6


****By inspecting this file I learned that:
****

1. "awk" is great for inspecting the number of columns (986). The number of columns is the same for the beginning and end of the dataset.
2. "wc" is a great for inspecting the number of rows
3. File size is 6.1M
4. This dataset does not have any non-ASCII characters which is great!
5. fang_et_al dataset has headers


###Attributes of the snp_position.txt dataset
**To inspect the snp_position.txt dataset, the same tools used for the fang_et_al_genotypes.txt dataset were used as shown.**

Using “head” to check the first 5 rows of the snp_position.txt dataset

```
$ head -n 5 snp_position.txt
```

**Comment:** The output is till hard to understand because of a very elaborate output.


Using head to show only the columns 1-10. This also answers the question: Does the snp_position.txt dataset have headers?

```
$ head -5 snp_position.txt | cut -f 1-10 | column -t
```

**Comment:** Yes this dataset has headers as listed here:
SNP_ID    cdv_marker_id  Chromosome  Position   alt_pos  mult_positions  amplicon  cdv_map_feature.name  gene  candidate/random


Using "awk" to inspect the number of coulumns for the snp_position.txt dataset

```
$ head -n 5 snp_position.txt | awk -F "\t" '{print NF; exit}'
```
Answer: 15 columns


Using "tail" to inspect the end of the snp_position.txt

```
$ tail -n 5 snp_position.txt | awk -F "\t" '{print NF; exit}'
```

**Comment:** After using the tail command, it showed that there were 15 columns at the end of the file which is great.


Using “wc” to check the first 5 rows of the snp_position.txt dataset

```
$ wc -l snp_position.txt
```

**Answer:** snp_position.txt has 984 rows.


Another question is what the file size is. Using "du", this question was answered.

```
$ du -h snp_position.txt
```

**Answer:** snp_position.txt is 38K


Checking the fang et al dataset to determine if it has any non-ASCII characters

```
$ file snp_position.txt 
``` 

**Comment:** snp_position.txt did not have any hidden non-ASCII characters.

**By inspecting this file I learned that:**

1. "awk" is suitable for inspecting the number of columns (15). The number of columns is the same for the beginning and end of the dataset.
2. snp_position.txt has headers
3. "wc" is great for inspecting the number of rows
4. File size is 38K
5. This dataset does not have any non-ASCII characters which is great!



##Data Processing

### Processing snp_position.txt dataset

To process the files, a couple of steps were followed:

**Step 1**: Prepared the SNP file by taking the three columns of interest (1,3 and 4).

```
$ cut -f 1,3,4 snp_position.txt > snp_formatted_position.txt
```
Comment: The three columns were successfully extracted.


**Step 2**: Sorted the newly created snp_formatted_position.txt file without including the header

```
$ (head -n 1 snp_formatted_position.txt && tail -n +2 snp_formatted_position.txt | sort -k1,1) > snp_formatted_positionsort.txt
```

**Comment**: This created a new file named: "snp_formatted_positionsort.txt". This file was double checked using to make sure it did not have a header using the code:

```
$ head snp_formatted_positionsort.txt
```
It looked great!

**Step 3**: Checked to see of the file was correctly sorted.
```
$ sort -c -k1,1 snp_formatted_positionsort.txt.
```

```
$ tail -n +2 snp_formatted_positionsort.txt | sort -c -k1,1
```

Comment: Both the above codes gave disorders which was expected. The snp_formatted_positionsort.txt file is now ready for joining to the fang. et.al. genotypes dataset which is not yet prepared. 


### Processing fang_et_al_genotypes dataset
**Step 1**: Since we needed to know more about specific groups in the dataset, we need to print the groups we have and the total number of groups using the codes below:

```
$ sort -k3 fang_et_al_genotypes.txt | cut -f 3 | uniq -c | column -t
```

```
$ sort -k3 fang_et_al_genotypes.txt | cut -f 3 | uniq -c | column -t | wc -l
```

**Comment**: The first code listed the groups and the second showed that we also have 17 groups in total.


**Step 2**: Extracted the 3 columns of interest for maize and teosinte from the fang_et_al_genotypes.txt dataset while excluding the first three columns

a) Code for maize
```
$ awk -F "\t" '$3 ~ /ZMMIL|ZMMLR|ZMMMR|Group/' fang_et_al_genotypes.txt | cut -f 1,4-986 > maize_genotypes.txt
```

b) Code for teosinte
```
$ awk -F "\t" '$3 ~ /ZMPBA|ZMPIL|ZMPJA|Group/' fang_et_al_genotypes.txt | cut -f 1,4-986 > teosinte_genotypes.txt
```


**Step 3**: Checked how many lines are there in maize_genotypes.txt and teosinte_genotypes.txt using:

```
$ wc -l maize_genotypes.txt teosinte_genotypes.txt
```

**Comment**: maize_genotypes.txt has 1574 lines while teosinte_genotypes.txt had 976 lines.


**Step 4**: Transposed each newly created dataset

a) Code for maize_genotypes.txt transposition

```
$ awk -f transpose.awk maize_genotypes.txt > transposed_maize_genotypes.txt
```
b) Code for teosinte_genotypes.txt transposition

```
$ awk -f transpose.awk teosinte_genotypes.txt > transposed_teosinte_genotypes.txt
```

**Step 5**: Sorted the transposed_maize_genotypes.txt and transposed_teosinte_genotypes.txt files without attaching the header using the codes and creating new files:

a) Code for maize
```
$ (head -n 1 transposed_maize_genotypes.txt && tail -n +2 transposed_maize_genotypes.txt | sort -k1,1) > transposed_maize_genotypes_sorted.txt
```

b) Code for teosinte
```
$ (head -n 1 transposed_teosinte_genotypes.txt && tail -n +2 transposed_teosinte_genotypes.txt | sort -k1,1) > transposed_teosinte_genotypes_sorted.txt
```

**Step 6**: Checking the new sorted files for both maize and teosinte to make sure that they are correctly sorted. 

a) Code for maize
```
$ tail -n +2 transposed_maize_genotypes_sorted.txt | sort -c -k1,1
```

b) Code for teosinte
```
$ tail -n +2 transposed_teosinte_genotypes_sorted.txt | sort -c -k1,1
```
**Comment**: There were no disorders for both files meaning that they were correctly sorted! Great news!


**Note**: Now that all our files were ready i.e. snp_formatted_positionsort.txt, transposed_maize_genotypes_sorted.txt, and transposed_teosinte_genotypes_sorted.txt, the next step was to use "join" to separately join the two "genotypes" files with the snp file. This was done separately for maize and teosinte files as shown below:

**A**: Joining transposed_maize_genotypes_sorted.txt with snp_formatted_positionsort.txt

a) "Join" code for maize:
```
$ join --header -1 1 -2 1 -t $'\t' snp_formatted_positionsort.txt transposed_maize_genotypes_sorted.txt > maize_snp_genotypes_joined.txt
```

Checking the maize_snp_genotypes_joined.txt file to make sure it was successfully done.
```
$ head maize_snp_genotypes_joined.txt | cut -f 1-10 | column -t
```
Comment: the maize_snp_genotypes_joined.txt file was arranged as expected with header for the first column being the SNP_ID, second column chromosome and third column was the Position. The rest of the columns have the genotypes.


**B**: Joining transposed_teosinte_genotypes_sorted.txt with snp_formatted_positionsort.txt

b) "Join" code for teosinte

```
$ join --header -1 1 -2 1 -t $'\t' snp_formatted_positionsort.txt transposed_teosinte_genotypes_sorted.txt > teosinte_snp_genotypes_joined.txt
```

Checking the teosinte_snp_genotypes_joined.txt file to make sure it was successfully done.

```
$ head teosinte_snp_genotypes_joined.txt | cut -f 1-10 | column -t
```
Comment: the teosinte_snp_genotypes_joined.txt file was arranged as expected with header for the first column being the SNP_ID, second column chromosome and third column was the Position. The rest of the columns have the genotypes.


**Another file inspection step**: To confirm the number of columns in the joined files and compare with what we expected to get.

a) maize_snp_genotypes_joined.txt
```
$ awk -F "\t" '{print NF; exit}' maize_snp_genotypes_joined.txt
```
**Comment**: Anwer was 1576 columns which was expected.


b) teosinte_snp_genotypes_joined.txt 
```
$ awk -F "\t" '{print NF; exit}' teosinte_snp_genotypes_joined.txt
```
**Comment**: Anwer was 978 coulmns which was expected.


Now that all files were ready, the next step was to create the files needed using the codes below:


**Creating the 22 files for maize**

a) 10 files (one for each chromosome) with SNPs ordered based on increasing position and missing values denoted as: ?

Codes for all 10 chromosomes
```
$ (head -n 1 maize_snp_genotypes_joined.txt && awk '$2 == 1' maize_snp_genotypes_joined.txt | sort -k 3n) > Chromosome1_maize_genotypes.txt
```
```
$ (head -n 1 maize_snp_genotypes_joined.txt && awk '$2 == 2' maize_snp_genotypes_joined.txt | sort -k 3n) > Chromosome2_maize_genotypes.txt
```
```
$ (head -n 1 maize_snp_genotypes_joined.txt && awk '$2 == 3' maize_snp_genotypes_joined.txt | sort -k 3n) > Chromosome3_maize_genotypes.txt
```
```
$ (head -n 1 maize_snp_genotypes_joined.txt && awk '$2 == 4' maize_snp_genotypes_joined.txt | sort -k 3n) > Chromosome4_maize_genotypes.txt
```
```
$ (head -n 1 maize_snp_genotypes_joined.txt && awk '$2 == 5' maize_snp_genotypes_joined.txt | sort -k 3n) > Chromosome5_maize_genotypes.txt
```
```
$ (head -n 1 maize_snp_genotypes_joined.txt && awk '$2 == 6' maize_snp_genotypes_joined.txt | sort -k 3n) > Chromosome6_maize_genotypes.txt
```
```
$ (head -n 1 maize_snp_genotypes_joined.txt && awk '$2 == 7' maize_snp_genotypes_joined.txt | sort -k 3n) > Chromosome7_maize_genotypes.txt
```
```
$ (head -n 1 maize_snp_genotypes_joined.txt && awk '$2 == 8' maize_snp_genotypes_joined.txt | sort -k 3n) > Chromosome8_maize_genotypes.txt
```
```
$ (head -n 1 maize_snp_genotypes_joined.txt && awk '$2 == 9' maize_snp_genotypes_joined.txt | sort -k 3n) > Chromosome9_maize_genotypes.txt
```
```
$ (head -n 1 maize_snp_genotypes_joined.txt && awk '$2 == 10' maize_snp_genotypes_joined.txt | sort -k 3n) > Chromosome10_maize_genotypes.txt
```

Note after creating files, the code below was used to check each file and make sure that the correct chromosomes were listed for each file

```
$ head ChromosomeX_maize_genotypes.txt | cut -f 1-10 | column -t
```
**Comment**: In the code for the above 'X" stands for each chromosome. Each file was okay.


b) 10 files (one for each chromosome) with SNPs ordered based on decreasing position and missing values denoted as: -

Codes for all 10 chromosomes
```
$ (head -n 1 maize_snp_genotypes_joined.txt &&awk '$2 == 1' maize_snp_genotypes_joined.txt | sort -k 3nr | sed 's/?/-/g') > Chromosome1_maize_reversed.txt
```
```
$ (head -n 1 maize_snp_genotypes_joined.txt &&awk '$2 == 2' maize_snp_genotypes_joined.txt | sort -k 3nr | sed 's/?/-/g') > Chromosome2_maize_reversed.txt
```
```
$ (head -n 1 maize_snp_genotypes_joined.txt &&awk '$2 == 3' maize_snp_genotypes_joined.txt | sort -k 3nr | sed 's/?/-/g') > Chromosome3_maize_reversed.txt
```
```
$ (head -n 1 maize_snp_genotypes_joined.txt &&awk '$2 == 4' maize_snp_genotypes_joined.txt | sort -k 3nr | sed 's/?/-/g') > Chromosome4_maize_reversed.txt
```
```
$ (head -n 1 maize_snp_genotypes_joined.txt &&awk '$2 == 5' maize_snp_genotypes_joined.txt | sort -k 3nr | sed 's/?/-/g') > Chromosome5_maize_reversed.txt
```
```
$ (head -n 1 maize_snp_genotypes_joined.txt &&awk '$2 == 6' maize_snp_genotypes_joined.txt | sort -k 3nr | sed 's/?/-/g') > Chromosome6_maize_reversed.txt
```
```
$ (head -n 1 maize_snp_genotypes_joined.txt &&awk '$2 == 7' maize_snp_genotypes_joined.txt | sort -k 3nr | sed 's/?/-/g') > Chromosome7_maize_reversed.txt
```
```
$ (head -n 1 maize_snp_genotypes_joined.txt &&awk '$2 == 8' maize_snp_genotypes_joined.txt | sort -k 3nr | sed 's/?/-/g') > Chromosome8_maize_reversed.txt
```
```
$ (head -n 1 maize_snp_genotypes_joined.txt &&awk '$2 == 9' maize_snp_genotypes_joined.txt | sort -k 3nr | sed 's/?/-/g') > Chromosome9_maize_reversed.txt
```
```
$ (head -n 1 maize_snp_genotypes_joined.txt &&awk '$2 == 10' maize_snp_genotypes_joined.txt | sort -k 3nr | sed 's/?/-/g') > Chromosome10_maize_reversed.txt
```

Note after creating files, the code below was used to check each file and make sure that the correct chromosomes were listed for each file

```
$ head ChromosomeX_maize_reversed.txt | cut -f 1-10 | column -t
```

**Comment**: In the code for the above 'X" stands for each chromosome. Each file was as expected.


c) Creating 1 file for all SNPs with unknown positions in the genome
```
$ (head -n 1 maize_snp_genotypes_joined.txt &&awk '$3 ~ /unknown/' maize_snp_genotypes_joined.txt) > maize_unknown_snp.txt
```

Code used to check contents of newly created file maize_unknown_snp.txt
```
$ head maize_unknown_snp.txt | cut -f 1-10 | column -t
```

**Comment**: This file had both the chromosome and positions of the SNPs listed as unknown.


d) Creating 1 file for all SNPs with multiple positions in the genome
```
$ (head -n 1 maize_snp_genotypes_joined.txt && awk '$3 ~ /multiple/' maize_snp_genotypes_joined.txt) > maize_multiple_snp.txt
```

Code used to check contents of newly created file maize_multiple_snp.txt
```
$ head maize_multiple_snp.txt | cut -f 1-10 | column -t
```
**Comment**: This file had positions of the SNPs listed as multiple but respective chromosomes were listed too.


**Creating the 22 files for teosinte**

a) 10 files (one for each chromosome) with SNPs ordered based on increasing position and missing values denoted as: ?

Codes for all 10 chromosomes
```
$ (head -n 1 teosinte_snp_genotypes_joined.txt && awk '$2 == 1' teosinte_snp_genotypes_joined.txt | sort -k 3n) > Chromosome1_teosinte_genotypes.txt
```
```
$ (head -n 1 teosinte_snp_genotypes_joined.txt && awk '$2 == 2' teosinte_snp_genotypes_joined.txt | sort -k 3n) > Chromosome2_teosinte_genotypes.txt
```
```
$ (head -n 1 teosinte_snp_genotypes_joined.txt && awk '$2 == 3' teosinte_snp_genotypes_joined.txt | sort -k 3n) > Chromosome3_teosinte_genotypes.txt
```
```
$ (head -n 1 teosinte_snp_genotypes_joined.txt && awk '$2 == 4' teosinte_snp_genotypes_joined.txt | sort -k 3n) > Chromosome4_teosinte_genotypes.txt
```
```
$ (head -n 1 teosinte_snp_genotypes_joined.txt && awk '$2 == 5' teosinte_snp_genotypes_joined.txt | sort -k 3n) > Chromosome5_teosinte_genotypes.txt
```
```
$ (head -n 1 teosinte_snp_genotypes_joined.txt && awk '$2 == 6' teosinte_snp_genotypes_joined.txt | sort -k 3n) > Chromosome6_teosinte_genotypes.txt
```
```
$ (head -n 1 teosinte_snp_genotypes_joined.txt && awk '$2 == 7' teosinte_snp_genotypes_joined.txt | sort -k 3n) > Chromosome7_teosinte_genotypes.txt
```
```
$ (head -n 1 teosinte_snp_genotypes_joined.txt && awk '$2 == 8' teosinte_snp_genotypes_joined.txt | sort -k 3n) > Chromosome8_teosinte_genotypes.txt
```
```
$ (head -n 1 teosinte_snp_genotypes_joined.txt && awk '$2 == 9' teosinte_snp_genotypes_joined.txt | sort -k 3n) > Chromosome9_teosinte_genotypes.txt
```
```
$ (head -n 1 teosinte_snp_genotypes_joined.txt && awk '$2 == 10' teosinte_snp_genotypes_joined.txt | sort -k 3n) > Chromosome10_teosinte_genotypes.txt
```

Note after creating files, the code below was used to check each file and make sure that the correct chromosomes were listed for each file

```
$ head ChromosomeX_teosinte_genotypes.txt | cut -f 1-10 | column -t
```
**Comment**: In the code for the above 'X" stands for each chromosome. Each file was okay.


b) 10 files (one for each chromosome) with SNPs ordered based on decreasing position and missing values denoted as: -

Codes for all 10 chromosomes
```
$ (head -n 1 teosinte_snp_genotypes_joined.txt &&awk '$2 == 1' teosinte_snp_genotypes_joined.txt | sort -k 3nr | sed 's/?/-/g') > Chromosome1_teosinte_reversed.txt
```
```
$ (head -n 1 teosinte_snp_genotypes_joined.txt &&awk '$2 == 2' teosinte_snp_genotypes_joined.txt | sort -k 3nr | sed 's/?/-/g') > Chromosome2_teosinte_reversed.txt
```
```
$ (head -n 1 teosinte_snp_genotypes_joined.txt &&awk '$2 == 3' teosinte_snp_genotypes_joined.txt | sort -k 3nr | sed 's/?/-/g') > Chromosome3_teosinte_reversed.txt
```
```
$ (head -n 1 teosinte_snp_genotypes_joined.txt &&awk '$2 == 4' teosinte_snp_genotypes_joined.txt | sort -k 3nr | sed 's/?/-/g') > Chromosome4_teosinte_reversed.txt
```
```
$ (head -n 1 teosinte_snp_genotypes_joined.txt &&awk '$2 == 5' teosinte_snp_genotypes_joined.txt | sort -k 3nr | sed 's/?/-/g') > Chromosome5_teosinte_reversed.txt
```
```
$ (head -n 1 teosinte_snp_genotypes_joined.txt &&awk '$2 == 6' teosinte_snp_genotypes_joined.txt | sort -k 3nr | sed 's/?/-/g') > Chromosome6_teosinte_reversed.txt
```
```
$ (head -n 1 teosinte_snp_genotypes_joined.txt &&awk '$2 == 7' teosinte_snp_genotypes_joined.txt | sort -k 3nr | sed 's/?/-/g') > Chromosome7_teosinte_reversed.txt
```
```
$ (head -n 1 teosinte_snp_genotypes_joined.txt &&awk '$2 == 8' teosinte_snp_genotypes_joined.txt | sort -k 3nr | sed 's/?/-/g') > Chromosome8_teosinte_reversed.txt
```
```
$ (head -n 1 teosinte_snp_genotypes_joined.txt &&awk '$2 == 9' teosinte_snp_genotypes_joined.txt | sort -k 3nr | sed 's/?/-/g') > Chromosome9_teosinte_reversed.txt
```
```
$ (head -n 1 teosinte_snp_genotypes_joined.txt &&awk '$2 == 10' teosinte_snp_genotypes_joined.txt | sort -k 3nr | sed 's/?/-/g') > Chromosome10_teosinte_reversed.txt
```

Note after creating files, the code below was used to check each file and make sure that the correct chromosomes were listed for each file

```
$ head ChromosomeX_teosinte_reversed.txt | cut -f 1-10 | column -t
```
**Comment**: In the code for the above 'X" stands for each chromosome. Each file was as expected.


c) Creating 1 file for all SNPs with unknown positions in the genome
```
$ (head -n 1 teosinte_snp_genotypes_joined.txt &&awk '$3 ~ /unknown/' teosinte_snp_genotypes_joined.txt) > teosinte_unknown_snp.txt
```

Code used to check contents of newly created file teosinte_unknown_snp.txt
```
$ head teosinte_unknown_snp.txt | cut -f 1-10 | column -t
```
**Comment**: This file had both the chromosome and positions of the SNPs listed as unknown.



d) Creating 1 file for all SNPs with multiple positions in the genome
```
$ (head -n 1 teosinte_snp_genotypes_joined.txt && awk '$3 ~ /multiple/' teosinte_snp_genotypes_joined.txt) > teosinte_multiple_snp.txt
```

Code used to check contents of newly created file teosinte_multiple_snp.txt
```
$ head teosinte_multiple_snp.txt | cut -f 1-10 | column -t
```
**Comment**: This file had positions of the SNPs listed as multiple but respective chromosomes were listed too.











# MarthaIbore-UNIX-assignment
