#Homework 4
Erika Lin  
05/03/19

Create a RNA seq pipeline.    
Objective: should be less than 1 hour

**PBS Script fastqc**

```
#####You can even separate each of these fastqc analysis into separate fastqc jobs (i.e. qcKO01, qcKO02, etc...) 

nano fastqc.pbs

#!/bin/bash

#PBS -N fastqc
#PBS -l mem=12gb,nodes=1:ppn=8,walltime=2:00:00 #2 hours
#PBS -e error-log-${PBS_JOBID}.txt
#PBS -o output-log-${PBS_JOBID}.txt

#-------------------------------------------------------------
# set working directory, resource directory, and load modules 
#-------------------------------------------------------------

cd "$PBS_O_WORKDIR"

fastq_dir="/gpfs/data/mscbmi/rnaseq/fastq"
reference_dir="/gpfs/data/mscbmi/rnaseq/hg38"

#3 modules needed for quality control 
module load gcc/6.2.0 
module load java-jdk/1.8.0_92 
module load fastqc/0.11.5 

#-----------------------
# Quality Control Using FastQC (output html)
#-----------------------

mkdir RawReadQC/

fastqc \ #call fastqc 
	--extract \
	-o RawReadQC/ \
	-t 6 \
	--nogroup \
	${fastq_dir}/KO01.fastq.gz \
	${fastq_dir}/KO02.fastq.gz \
	${fastq_dir}/KO03.fastq.gz \
	${fastq_dir}/WT01.fastq.gz \
	${fastq_dir}/WT02.fastq.gz \
	${fastq_dir}/WT03.fastq.gz
	
qsub fastqc.pbs

```

**PBS Script KO1**

```
nano KO01.pbs

#!/bin/bash

#PBS -N KO1
#PBS -l mem=12gb,nodes=1:ppn=8,walltime=8:00:00
#PBS -e error-log-${PBS_JOBID}.txt
#PBS -o output-log-${PBS_JOBID}.txt

#-------------------------------------------------------------
# set working directory, resource directory, and load modules 
#-------------------------------------------------------------

cd "$PBS_O_WORKDIR"

fastq_dir="/gpfs/data/mscbmi/rnaseq/fastq"
reference_dir="/gpfs/data/mscbmi/rnaseq/hg38"

#2 modules needed for Kallisto
module load gcc/6.2.0
module load kallisto/0.44.0


#-----------------------
# Transcriptional Quantification Using Psuedo-alignment Tool kallisto
#-----------------------

mkdir -p \
	Quant/KO01

kallisto \
	quant \
	--index=${reference_dir}/transcripts.idx \
	--output-dir=Quant/KO01 \
	--single \
	--fragment-length=180 \
	--sd=20 \
	--threads=8 \
	${fastq_dir}/KO01.fastq.gz
	
qsub KO01.pbs
	
```	
	
**PBS Script KO2**	

	
```
nano KO02.pbs

#!/bin/bash

#PBS -N KO2 #name
#PBS -l mem=12gb,nodes=1:ppn=8,walltime=8:00:00
#PBS -e error-log-${PBS_JOBID}.txt
#PBS -o output-log-${PBS_JOBID}.txt

#-------------------------------------------------------------
# set working directory, resource directory, and load modules 
#-------------------------------------------------------------

cd "$PBS_O_WORKDIR"

fastq_dir="/gpfs/data/mscbmi/rnaseq/fastq"
reference_dir="/gpfs/data/mscbmi/rnaseq/hg38"


#2 modules needed for Kallisto
module load gcc/6.2.0
module load kallisto/0.44.0

#-----------------------
# Transcriptional Quantification Using Psuedo-alignment Tool kallisto
#-----------------------

mkdir -p \
Quant/KO02

kallisto \
	quant \
	--index=${reference_dir}/transcripts.idx \
	--output-dir=Quant/KO02 \
	--single \
	--fragment-length=180 \
	--sd=20 \
	--threads=8 \
	${fastq_dir}/KO02.fastq.gz
	
qsub KO02.pbs
```
**PBS Script KO3**

```
nano KO03.pbs

#!/bin/bash

#PBS -N KO3
#PBS -l mem=12gb,nodes=1:ppn=8,walltime=8:00:00
#PBS -e error-log-${PBS_JOBID}.txt
#PBS -o output-log-${PBS_JOBID}.txt

#-------------------------------------------------------------
# set working directory, resource directory, and load modules 
#-------------------------------------------------------------

cd "$PBS_O_WORKDIR"

fastq_dir="/gpfs/data/mscbmi/rnaseq/fastq"
reference_dir="/gpfs/data/mscbmi/rnaseq/hg38"


#2 modules needed for Kallisto
module load gcc/6.2.0
module load kallisto/0.44.0


#-----------------------
# Transcriptional Quantification Using Psuedo-alignment Tool kallisto
#-----------------------

mkdir -p \
	Quant/KO03

kallisto \
	quant \
	--index=${reference_dir}/transcripts.idx \
	--output-dir=Quant/KO03 \
	--single \
	--fragment-length=180 \
	--sd=20 \
	--threads=8 \
	${fastq_dir}/KO03.fastq.gz

qsub KO03.pbs
	
```
	
**PBS Script WT1**

```
nano WT01.pbs

#!/bin/bash

#PBS -N WT1
#PBS -l mem=12gb,nodes=1:ppn=8,walltime=8:00:00
#PBS -e error-log-${PBS_JOBID}.txt
#PBS -o output-log-${PBS_JOBID}.txt

#-------------------------------------------------------------
# set working directory, resource directory, and load modules 
#-------------------------------------------------------------

cd "$PBS_O_WORKDIR"

fastq_dir="/gpfs/data/mscbmi/rnaseq/fastq"
reference_dir="/gpfs/data/mscbmi/rnaseq/hg38"


#2 modules needed for Kallisto
module load gcc/6.2.0
module load kallisto/0.44.0

#-----------------------
# Transcriptional Quantification Using Psuedo-alignment Tool kallisto
#-----------------------


mkdir -p \
	Quant/WT01

kallisto \
	quant \
	--index=${reference_dir}/transcripts.idx \
	--output-dir=Quant/WT01 \
	--single \
	--fragment-length=180 \
	--sd=20 \
	--threads=8 \
	${fastq_dir}/WT01.fastq.gz
	
qsub WT01.pbs
	
```

**PBS Script WT2**

```
nano WT02.pbs

#!/bin/bash

#PBS -N WT2
#PBS -l mem=12gb,nodes=1:ppn=8,walltime=8:00:00
#PBS -e error-log-${PBS_JOBID}.txt
#PBS -o output-log-${PBS_JOBID}.txt

#-------------------------------------------------------------
# set working directory, resource directory, and load modules 
#-------------------------------------------------------------

cd "$PBS_O_WORKDIR"

fastq_dir="/gpfs/data/mscbmi/rnaseq/fastq"
reference_dir="/gpfs/data/mscbmi/rnaseq/hg38"


#2 modules needed for Kallisto
module load gcc/6.2.0
module load kallisto/0.44.0

#-----------------------
# Transcriptional Quantification Using Psuedo-alignment Tool kallisto
#-----------------------

mkdir -p \
	Quant/WT02

kallisto \
	quant \
	--index=${reference_dir}/transcripts.idx \
	--output-dir=Quant/WT02 \
	--single \
	--fragment-length=180 \
	--sd=20 \
	--threads=8 \
	${fastq_dir}/WT02.fastq.gz
	
qsub WT02.pbs

```

**PBS Script WT3**

```
nano WT03.pbs

#!/bin/bash

#PBS -N WT3
#PBS -l mem=12gb,nodes=1:ppn=8,walltime=8:00:00
#PBS -e error-log-${PBS_JOBID}.txt
#PBS -o output-log-${PBS_JOBID}.txt

#-------------------------------------------------------------
# set working directory, resource directory, and load modules 
#-------------------------------------------------------------

cd "$PBS_O_WORKDIR"

fastq_dir="/gpfs/data/mscbmi/rnaseq/fastq"
reference_dir="/gpfs/data/mscbmi/rnaseq/hg38"

#2 modules needed for Kallisto
module load gcc/6.2.0
module load kallisto/0.44.0

#-----------------------
# Transcriptional Quantification Using Psuedo-alignment Tool kallisto
#-----------------------

mkdir -p \
	Quant/WT03

kallisto \
	quant \
	--index=${reference_dir}/transcripts.idx \
	--output-dir=Quant/WT03 \
	--single \
	--fragment-length=180 \
	--sd=20 \
	--threads=8 \
	${fastq_dir}/WT03.fastq.gz

qsub WT03.pbs

```
**tximport R PBS Script**

```
nano tximport.pbs


#!/bin/bash

#PBS -N tximport
#PBS -l mem=12gb,nodes=1:ppn=8,walltime=8:00:00
#PBS -e error-log-${PBS_JOBID}.txt
#PBS -o output-log-${PBS_JOBID}.txt

#-------------------------------------------------------------
# set working directory, resource directory, and load modules 
#-------------------------------------------------------------

cd "$PBS_O_WORKDIR"

fastq_dir="/gpfs/data/mscbmi/rnaseq/fastq"
reference_dir="/gpfs/data/mscbmi/rnaseq/hg38"

#2 modules needed for R transcript summary
module load gcc/6.2.0
module load R/3.5.0

#-----------------------
# Summarization of Expression from Transcripts to Genes
#-----------------------

#submit job 2 only after job1 has finished
tximport.pbs=$(qsub -W depend=afterok:$KO01.pbs:$KO02.pbs:$KO03.pbs:$WT01.pbs:$WT02.pbs:$WT03.pbs tximport.pbs)
echo $tximport.pbs

Rscript --vanilla \
	/gpfs/data/mscbmi/rnaseq/SRC/tximport.R
	
qsub tximport.pbs

```
**PCA R PBS Script**

```
nano PCA.pbs
#!/bin/bash

#PBS -N pca
#PBS -l mem=12gb,nodes=1:ppn=8,walltime=8:00:00
#PBS -e error-log-${PBS_JOBID}.txt
#PBS -o output-log-${PBS_JOBID}.txt

#-------------------------------------------------------------
# set working directory, resource directory, and load modules 
#-------------------------------------------------------------

cd "$PBS_O_WORKDIR"

fastq_dir="/gpfs/data/mscbmi/rnaseq/fastq"
reference_dir="/gpfs/data/mscbmi/rnaseq/hg38"

#2 modules needed for PCA
module load gcc/6.2.0
module load R/3.5.0

#-----------------------
# Sample Association Using PCA Plot
#-----------------------

#submit job 2 only after job 1 has finished
pca.pbs=$(qsub -W depend=afterok:$KO01.pbs:$KO02.pbs:$KO03.pbs:$WT01.pbs:$WT02.pbs:$WT03.pbs pca.pbs)
echo $pca.pbs #pca will not run if everything before has not been loaded

Rscript --vanilla \
	/gpfs/data/mscbmi/rnaseq/SRC/pca.R
	
qsub pca.pbs
	
```

**Create a BASH script to execute entire pipeline**

```
nano pipeline.sh

#!/bin/bash

#########################
#run qc
qsub fastqc.pbs

#run transcriptional quantification of KO/WT 01-03 
qsub KO01.pbs
qsub KO02.pbs
qsub KO03.pbs
qsub WT01.pbs
qsub WT02.pbs
qsub WT03.pbs

#summarization of expression from transcripts to genes 
qsub tximport.pbs

#sample association using PCA plot
qsub pca.pbs
```

Execute the entire BASH script for RNA sequence pipeline

```
chmod u+x pipeline.sh
./pipeline.sh

```

**Move to Desktop**

Open a new terminal:

```
cd Desktop

scp t.cri.biowksp16@gardner.cri.uchicago.edu:~/rnaseq/Quant/tutorial.count.kallisto.txt ./
scp t.cri.biowksp16@gardner.cri.uchicago.edu:~/rnaseq/Quant/tutorial.pca.pdf ./

