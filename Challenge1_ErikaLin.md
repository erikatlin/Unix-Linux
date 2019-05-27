###Week 1 Challenge: Using AWK calculate the average for each column individually of file: expression.txt 

cp /gpfs/data/mscbmi/lecture1/expression.txt . 
head -20 expression.txt
wc -l expression.txt

    Copy expression.txt from the repository to home directory. 
    Take a peak at the first 20 lines of the expression.txt file.
    Count the # of lines in the file. 
    
Tumor Replicate 1 (column 2) average: 
awk '{x+=$2}END{print x/NR}' expression.txt 

    Add up all expression values in column 2 and set as x. Take x and divide by the total rows.
    
    4.38797 

Tumor Replicate 2 (column 3) average: 
awk '{x+=$3}END{print x/NR}' expression.txt

    4.30238

Tumor Replicate 3 (column 4) average:
awk '{x+=$4}END{print x/NR}' expression.txt

    4.3913

Normal Replicate 1 (column 5) average:
awk '{x+=$5}END{print x/NR}' expression.txt

    4.39687
    
Normal Replicate 2 (column 6) average:
awk '{x+=$6}END{print x/NR}' expression.txt

    4.36515

Normal Replicate 3 (column 7) average:
awk '{x+=$7}END{print x/NR}' expression.txt

    4.38485

