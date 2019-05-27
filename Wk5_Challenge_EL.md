#Week 5 Challenge

Erika Lin  
May 10, 2019

```
mkdir R
cd R
cp /gpfs/data/mscbmi/hw4/*.fasta .
ls
```
reference.fasta  
shor_reference.fasta


**Week5Challenge R Script**

```
.libPaths(
  c(
    "/home/t.cri.biowksp16/R",
    .libPaths()
  )
)

setwd("/home/t.cri.biowksp16/R")

library(seqinr)
library(Biostrings)

seqsim <- function(seq)
{
  sink("alignment.txt")
  for (i in 1:(length(seq)-1))
  {
    n1 <- toupper(c2s(seq[i]))
    n2 <- toupper(c2s(seq[i+1]))
    globalAlign <- pairwiseAlignment(n1,n2)
    print(globalAlign)
  }
  sink()
}

seq <- read.fasta(file="reference.fasta")
start.time <- Sys.time()
seqsim(seq)
end.time <- Sys.time()
time.taken <- end.time - start.time
write.table(time.taken, "timestamp.txt")
```


**Move R script on Desktop to R folder on Terminal**

Open a new terminal 

```
scp /Users/erikalin/Desktop/Week5Challenge.R t.cri.biowksp16@gardner.cri.uchicago.edu:~/R/

```

**Create R PBS Script** 

```
nano sequence.pbs
```
```
#!/bin/bash

#PBS -N sequence
#PBS -l mem=12gb,nodes=1:ppn=8,walltime=00:30:00
#PBS -e error-log-${PBS_JOBID}.txt
#PBS -o output-log-${PBS_JOBID}.txt

#-------------------------------------------------------------
# set working directory, resource directory, and load modules
#-------------------------------------------------------------
cd "$PBS_O_WORKDIR"

ref_dir="/home/t.cri.biowksp16/R/reference.fasta"

module load gcc/6.2.0
module load R/3.5.0

#-------------------------------------------------------------
# Pairwise Alignment of Sequences
#-------------------------------------------------------------

Rscript --vanilla \
        /home/t.cri.biowksp16/R/Week5Challenge.R

#-------------------------------------------------------------
# Align sequence
#-------------------------------------------------------------
grep 'score' alignment.txt > score.txt
sort score.txt > newscore.txt
```
```      	
qsub sequence.pbs

```

**Maximum and minimum alignment scores:**

```
cd R
ls

head newscore.txt
tail newscore.txt 

```
Minimum alignment score: 12616.3   
Maximum alignment score: 29337.48 

**Record, report and explain the total runtime (execution time) for your script running with the following hardware configurations:**

```
head timestamp.txt
```
The time it took to run the analysis was: 36.6 minutes (this seems very long...)

Try with:

1 nodes; 4 cpus; 4gb of RAM (JOB ID: 552)  
1 nodes; 8 cpus; 4gb of RAM  JOB ID: 553)   
1 nodes; 16 cpus; 8gb of RAM (JOB ID: 555)

No apparent difference was observed with hardware configurations. 

**Are there opportunities for reducing the runtime using Parallelization? If so, what could be the strategy? Algorithm?**

I think that separating the original reference.fasta into smaller files can be more efficient. It is a big file as I could tell from just doing a check on the R script in terminal. Separate this .fasta into smaller files and create PBS scripts and then a final pipeline that will run them parallel to each other. 

