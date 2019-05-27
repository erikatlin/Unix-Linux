#Homework 6

May 18, 2019

Erika Lin

**R Code: Principal Component Analysis (PCA) of Transcript Quantification post Kallisto with comments**

```
setwd("~/rnaseq") #set working directory to rnaseq that was created last time in terminal 
library(ggplot2) #load ggplot to create plots
library(ggfortify) #load ggfortify to visualize statistically analyzed data (i.e. PCA)


data <- read.csv("Quant/tutorial.count.kallisto.txt", #this .txt file was generated when the PCA was done last time, was saved in Quant folder, so read it in and give the dataframe the name 'data'
  comment.char = "#", #comment with character '#' in the .txt file 
  sep = "\t", #separate the components in the .txt file with a space 
  check.names = FALSE #allows for duplicate column names
)
rownames(data) <- data[, 1] #index for all rows from Geneid column in 'data' dataframe and set them as row names in 'data' dataframe 
data <- data[, tail(1:ncol(data), 6)] #print all the rows and print all the columns BUT exclude the 'Geneid' column 
colnames(data) <- gsub(
  "^.+((KO|WT)\\d{2}).+$",
  "\\1", #replace all matches of a string and set them as column names in 'data' dataframe: KO01, KO02, KO03, WT01, WT02, WT03 
  colnames(data) #show the column names of data
)

#remove the low reads
data.rmLow <- data[ #from data if the x (# of transcripts expressed by that gene) is x >=10 put that into 'data.rmLow' dataframe and continue to do that in each row 
  apply(
    data,
    1,
    function(x) {
      all(x >= 10) 
    }
  ),
  ]

#normalization of the reads for KO01-KO03 & WT01-WT03
data.trans <- apply( #from 'data.rmLow' for each column multiply x (# of transcripts expressed by that gene) by 10^6 divided by sum(x) (sum of transcripts in KO01)
  data.rmLow,
  2,
  function(x) {
    10^6 * x / sum(x)
  }
)

mad <- apply( #create a new dataframe 'mad' where the log2 values in all the rows of 'data.trans' is taken 
  log2(data.trans),
  1,
  mad
)

top <- head( #create a new dataframe 'top' ordering top 500 transcript values in 'data.trans' in descending order
  data.trans[order(-mad), ],
  500
)

top.group <- cbind( #combines dataframe 'top' with created dataframe 'group'to create a final dataframe called 'top.group' bound by columns
  t(top), #transpose 'top' dataframe switching the x and y axis labels
  data.frame(
    group = gsub("\\d{2}", "", colnames(top)) #'group' dataframe takes initial column names (KO01, WT01, etc..) and puts them on y-axis, while the gene names of the top expressed genes are headers of each column 
  )
)


pdf(
  file = "Quant/tutorial1.pca.pdf", #create a pdf in the folder (Quant) on terminal with specific width/height parameters called tutorial1.pca.pdf
  width = 8,
  height = 8
)

autoplot( #plot the principal component analysis 
  prcomp(t(top)), 
  data = top.group,
  colour = 'group',
  shape = FALSE,
  label.size = 5
) +
  theme_minimal(base_size = 18)

dev.off() #clean up the environment and allows you to open up the created pdf


print(sessionInfo()) #prints information about R and used packages
q(save = "no") #quit R session and do not save

```

**Create pipeline to execute R script**

Move R script from Desktop to rnaseq folder in Terminal

```
scp /Users/erikalin/Desktop/Homework6_code.R t.cri.biowksp16@gardner.cri.uchicago.edu:~/rnaseq/

```

```
cd rnaseq
nano Rpipeline.pbs
```

Rpipeline.pbs

```
###!/bin/bash

#PBS -N Rpipeline
#PBS -l mem=8gb,nodes=1:ppn=16,walltime=00:30:00
#PBS -e error-log-${PBS_JOBID}.txt
#PBS -o output-log-${PBS_JOBID}.txt

#-------------------------------------------------------------
# set working directory, resource directory, and load modules
#-------------------------------------------------------------
cd "$PBS_O_WORKDIR"

module load gcc/6.2.0
module load R/3.5.0

#-------------------------------------------------------------
# Principal Component Analysis
#-------------------------------------------------------------
Rscript --vanilla \
        /home/t.cri.biowksp16/rnaseq/Homework6_code.R
```

```
qsub Rpipeline.pbs
watch qstat 
cd Quant
ls -lt
```
-rw-r----- 1 t.cri.biowksp16 t.cri.biowksp16    4618 May 14 15:57 tutorial1.pca.pdf

**Transfer generated tutorial1.pca.pdf from Terminal to Desktop**

```
scp t.cri.biowksp16@gardner.cri.uchicago.edu:~/rnaseq/Quant/tutorial1.pca.pdf /Users/erikalin/Desktop

```
