#Homework 2: Advanced Bioinformatics
###Erika Lin
###April 20, 2019

**1. Download the Generic Feature Format (GFF): Saccharomyces Cerevisiae yeast genome
**
**2.
a. How many genes are there in the data?  **
 	
 	cut -f 1,3,4,5 saccharomyces_cerevisiae.gff | grep -w gene | uniq -c | wc -l
 
6600
    
b. How many genes are there on chromosome 2?

	cut -f 1,3,4,5 saccharomyces_cerevisiae.gff | grep -w gene | uniq -c > genes_list.txt

	cat genes_list.txt | wc -l

	cat genes_list.txt | grep -w "chrII" > genes_list_chrII.txt 
	
	cat genes_list_chrII.txt | wc -l

 456
    
c. How many mRNAs are there on chromosome 10?

	cut -f 1,3,4,5 saccharomyces_cerevisiae.gff | grep -w mRNA | uniq -c > mRNA_list.txt
	
	cat mRNA_list.txt | wc -l
	
	cat mRNA_list.txt | grep -w "chrX" > mRNA_list_chrX.txt
	
	cat mRNA_list_chrX.txt | wc -l

 398
    
d. Describe what the following command do: sed '/#/d' saccharomyces_cerevisiae.gff > features.gff

	sed '/#/d' saccharomyces_cerevisiae.gff > features.gff
	grep "#" saccharomyces_cerevisiae.gff | wc -l
	20
	grep "#" features.gff | wc -l
	0
  
 Delete lines in GFF file that have #. The > indicates redirecting an output of a command to a file, so after deletions from saccharomyces_cerevisiae.gff print to features.gff.  
    
**e. How many features (lines) are there in the file features.gff**

	wc -l features.gff 
    
  175048


**f. Describe what the following command do: cut -f 1 features.gff | sort | uniq -c | sort -k1n**

  In features.gff: extract column 1, sort, and count unique lines. Sort first column in table in ascending order going down a column.  
    
    Column 1 = chrI-chrmt: sorted in ascending numerical order going down the column
    Sorted number of unique nucleotide sequences
    Length of chromosomes in features.gff from smallest to largest going down a column 
    
g. Which chromosome is the longest and which one is the shortest?
cut -f 1 features.gff | sort | uniq -c | sort -k1n | tail -17

        214 chrmt
    	  424 chrI
    	 532 chrVI
    	 700 chrIII
    	 838 chrIX
    	1118 chrVIII
    	1156 chrV
    	1171 chrXI
    	1389 chrX
    	1476 chrXIV
    	1556 chrII
    	1766 chrXIII
    	1770 chrXVI
    	2007 chrXV
    	2049 chrXII
    	2053 chrVII
    	2839 chrIV
grep -w chromosome saccharomyces_cerevisiae.gff | cut -f 1,5 | sort -k2n

    chrmt	85779
    chrI	230218
    chrVI	270161
    chrIII	316620
    chrIX	439888
    chrVIII	562643
    chrV	576874
    chrII	628309
    chrXI	666816
    chrX	745751
    chrXIV	784333
    chrII	813184
    chrXIII	924431
    chrXVI	948066
    chrXII	1078177
    chrVII	1090940
    chrXV	1091291
    chrIV	1531933
    
Longest: Chromosome IV
Shortest: Mitochondrial chromosome 

h. Describe what the following command do: sed 's/chrI/chr1/g' features.gff > new_features.gff

less new_features.gff (to check)

    Replace 'chrI' with 'chr1' globally in features.gff and print those changes to new_features.gff. 
    
3. Develop a Linux Shell script to generate the first n numbers of the Fibonacci series

		nano fibonacci.sh 
		------------------
		#! /bin/sh
		echo "How many numbers do you want to generate?"
		read n
		
		x1=0 #first number of Fibonacci Series
		x2=1 #second number of Fibonacci Series
		
		echo "Fibonacci Series is:"
		
		for(( i=0;i<n;i++ )) #for loop until n generated numbers of Fibonacci Series satisfied
		do
		echo "$x1"
		sum=$((x1+x2))
		
		x1=$x2
		x2=$sum
	
		done

		chmod u+x fibonacci.sh
		
		./fibonacci.sh

		    How many numbers do you want to generate?  
		    4  
		    Fibonacci Series is:  
		    0
		    1
		    1
		    2

**4. Measure and report the time it takes to execute your script**

	time fibonacci.sh
    
  real	0m0.009s  
  user	0m0.001s  
  sys	0m0.004s
