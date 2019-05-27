#Assignment 3 
Erika Lin  
4/27/19

**1) Create an analysis pipeline that efficiently performs the raw data QC for heart and kidney files, then perform the mapping of those files using both bwa and bowtie2 tools.** 

In the 'mscbmi' folder (batch mode):

	cd mscbmi
	mkdir hw
	cd ~/mscbmi/hw
	cp /gpfs/data/mscbmi/lecture1/*.gz ./
		

Create a PBS script with analysis pipeline for raw data QC for heart and kidney files.  

Heart:

	nano raw_heart.pbs
	#!/bin/bash
	###############################
	# Resource Manager Directions #
	###############################
	### Set the name of the job
	#PBS -N raw_heart.pbs
	### Select the shell you would like to script to execute within
	#PBS -S /bin/bash
	### Inform the scheduler of the expected runtime
	#PBS -l walltime=0:59:00
	### Inform the scheduler of the number of CPU cores for your job
	#PBS -l nodes=1:ppn=1
	### Inform the scheduler of the amount of memory you expect
	#PBS -l mem=512mb
	### Set the destination for your program's output and error
	#PBS -o $HOME/${PBS_JOBNAME}.o${PBS_JOBID}
	#PBS -e $HOME/${PBS_JOBNAME}.e${PBS_JOBID}
		
	###################
	# Job Execution #
	###################
	#Load modules
	module load gcc java-jdk
	module load fastqc
		
	#set paths
	seqPath=~/mscbmi/hw
	seqfile1=$seqPath/heart_ERR030886.sample.1.fastq.gz
	seqfile2=$seqPath/heart_ERR030886.sample.2.fastq.gz   
		
	qsub raw_heart.pbs
	qstat
		
	ls -l
Heart Files include:
	
	heart.fastqc.log 
	heart_ERR030886.sample.1_fastqc.html
	heart_ERR030886.sample.1_fastqc.zip
	heart_ERR030886.sample.2.fastq.gz
	heart_ERR030886.sample.2_fastqc.html
	heart_ERR030886.sample.2_fastqc.zip

Kidney:
		
	nano raw_kidney.pbs
		
	#!/bin/bash
	###############################
	# Resource Manager Directions #
	###############################
	### Set the name of the job
	#PBS -N raw_kidney.pbs
	### Select the shell you would like to script to execute within
	#PBS -S /bin/bash
	### Inform the scheduler of the expected runtime
	#PBS -l walltime=0:59:00
	### Inform the scheduler of the number of CPU cores for your job
	#PBS -l nodes=1:ppn=1
	### Inform the scheduler of the amount of memory you expect
	#PBS -l mem=512mb
	### Set the destination for your program's output and error
	#PBS -o $HOME/${PBS_JOBNAME}.o${PBS_JOBID}
	#PBS -e $HOME/${PBS_JOBNAME}.e${PBS_JOBID}
		
	###################
	# Job Execution #
	###################
	#Load modules
	module load gcc java-jdk
	module load fastqc
		
	#set paths
	seqPath=~/mscbmi/hw
	seqfile1=$seqPath/kidney_ERR030885.sample.1.fastq.gz
	seqfile2=$seqPath/kidney_ERR030885.sample.2.fastq.gz
		
	#run fast QC
	fastqc -o $seqPath $seqfile1 &> $seqPath\/kidney.fastqc.log
	fastqc -o $seqPath $seqfile2 &>> $seqPath\/kidney.fastqc.log

	qsub raw_kidney.pbs
	qstat
		
	ls -l
Kidney Files include:
	
	kidney.fastqc.log 
	kidney_ERR030885.sample.1_fastqc.html
	kidney_ERR030885.sample.1_fastqc.zip
	kidney_ERR030885.sample.2.fastq.gz
	kidney_ERR030885.sample.2_fastqc.html
	kidney_ERR030885.sample.2_fastqc.zip
	
Map files with 'bwa' and bowtie2' tools.

bwa Heart files:

	nano bwa_heart.pbs
		
	#!/bin/bash
	###############################
	# Resource Manager Direction #
	###############################
	### Set the name of the job
	#PBS -N bwa_heart
	### Select the shell you would like to script to execute within
	#PBS -S /bin/bash
	### Inform the scheduler of the expected runtime
	#PBS -l walltime=0:59:00
	### Inform the scheduler of the number of CPU cores for your job
	#PBS -l nodes=1:ppn=4
	### Inform the scheduler of the amount of memory you expect
	#PBS -l mem=2gb
	### Set the destination for your program's output and error
	#PBS -o $HOME/mscbmi/hw/${PBS_JOBNAME}.o${PBS_JOBID}
	#PBS -e $HOME/mscbmi/hw/${PBS_JOBNAME}.e${PBS_JOBID}
		
	#################
	# Job Execution #
	#################
	#Load modules
	module load gcc java-jdk
	module load bwa
	module load samtools
		
	#Set the input files and program paths
	seqPath=~/mscbmi/hw
	outPath=~/mscbmi/hw/bwa
	seqfile1=$seqPath/heart_ERR030886.sample.1.fastq.gz
	seqfile2=$seqPath/heart_ERR030886.sample.2.fastq.gz
	readgroup=$outPath/heart_ERR030886.sample
	referenceSeq=/group/referenceFiles/Homo_sapiens/UCSC/hg19/hg19.GATKbundle.1.5/u$
		
	#Create output directory if  it does not exist
	if [ ! -d $outPath ]; then mkdir $outPath; fi
		
	#Align pair-ended sequences, 4 threads
	bwa aln -t 4 $referenceSeq $seqfile1 >  $readgroup.1.sai
	bwa aln -t 4 $referenceSeq $seqfile2 >  $readgroup.2.sai
	bwa sampe $referenceSeq $readgroup.1.sai $readgroup.2.sai $seqfile1 $seqfile2 >$
		
	#Convert sam file to bam file
	samtools view -F 4 -Sb $readgroup.sam > $readgroup.bam
		
	#Sort and index bam file
	samtools sort $readgroup.bam -o $readgroup.sorted.bam
	samtools index $readgroup.sorted.bam
	
bwa Heart files include:

	heart_ERR030886.sample.1.sai
	heart_ERR030886.sample.sorted.bam
	heart_ERR030886.sample.2.sai
	heart_ERR030886.sample.sorted.bam.bai
	heart_ERR030886.sample.bam    
	heart_ERR030886.sample.sam    

bwa Kidney files

	nano bwa_kidney.pbs
	
	#!/bin/bash
	###############################
	# Resource Manager Direction #
	###############################
	### Set the name of the job
	#PBS -N bwa_heart
	### Select the shell you would like to script to execute within
	#PBS -S /bin/bash
	### Inform the scheduler of the expected runtime
	#PBS -l walltime=0:59:00
	### Inform the scheduler of the number of CPU cores for your job
	#PBS -l nodes=1:ppn=4
	### Inform the scheduler of the amount of memory you expect
	#PBS -l mem=2gb
	### Set the destination for your program's output and error
	#PBS -o $HOME/mscbmi/hw/${PBS_JOBNAME}.o${PBS_JOBID}
	#PBS -e $HOME/mscbmi/hw/${PBS_JOBNAME}.e${PBS_JOBID}
		
	#################
	# Job Execution #
	#################
	#Load modules
	module load gcc java-jdk
	module load bwa
	module load samtools
		
	#Set the input files and program paths
	seqPath=~/mscbmi/hw
	outPath=~/mscbmi/hw/bwa
	seqfile1=$seqPath/kidney_ERR030885.sample.1.fastq.gz
	seqfile2=$seqPath/kidney_ERR030885.sample.2.fastq.gz
	readgroup=$outPath/heart_ERR030885.sample
	referenceSeq=/group/referenceFiles/Homo_sapiens/UCSC/hg19/hg19.GATKbundle.1.5/u$
		
	#bwa output directory exists
		
	#Align pair-ended sequences, 4 threads
	bwa aln -t 4 $referenceSeq $seqfile1 >  $readgroup.1.sai
	bwa aln -t 4 $referenceSeq $seqfile2 >  $readgroup.2.sai
	bwa sampe $referenceSeq $readgroup.1.sai $readgroup.2.sai $seqfile1 $seqfile2 >$
		
	#Convert sam file to bam file
	samtools view -F 4 -Sb $readgroup.sam > $readgroup.bam
		
	#Sort and index bam file
	samtools sort $readgroup.bam -o $readgroup.sorted.bam
	samtools index $readgroup.sorted.bam
	
bwa Kidney files include:

	kidney_ERR030885.sample.1.sai
	kidney_ERR030885.sample.sorted.bam
	kidney_ERR030885.sample.2.sai
	kidney_ERR030885.sample.sorted.bam.bai
	kidney_ERR030885.sample.bam    
	kidney_ERR030885.sample.sam 


bowtie Heart files: 
	
	#!/bin/bash
	###############################
	# Resource Manager Directions #
	###############################
	### Set the name of the job
	#PBS -N bowtie_heart
	### Select the shell you would like to script to execute within
	#PBS -S /bin/bash
	### Inform the scheduler of the expected runtime
	#PBS -l walltime=0:59:00
	### Inform the scheduler of the number of CPU cores for your job
	#PBS -l nodes=1:ppn=4
	### Inform the scheduler of the amount of memory you expect
	#PBS -l mem=2gb
	### Set the destination for your program's output and error
	#PBS -o $HOME/mscbmi/hw/${PBS_JOBNAME}.o${PBS_JOBID}
	#PBS -e $HOME/mscbmi/hw/${PBS_JOBNAME}.e${PBS_JOBID}
	
	#################
	# Job Execution #
	#################
	#Load modules
	module load gcc java-jdk
	module load samtools bowtie2
	
	#Set the file and program paths
	seqPath=~/mscbmi/hw
	outPath=~/mscbmi/hw/bowtie2
	seqfile1=$seqPath/heart_ERR030886.sample.1.fastq.gz
	seqfile2=$seqPath/heart_ERR030886.sample.2.fastq.gz
	readgroup=$outPath/heart_ERR030886.sample
	referenceSeq=/group/referenceFiles/Homo_sapiens/UCSC/hg19/Sequence/IlluminaBowt$
	
	# create output directory if it does not exist
	if [ ! -d $outPath ]; then mkdir $outPath; fi
	
	#Align pair-ended sequences, 4 threads
	bowtie2 -p 4 -x $referenceSeq -1 $seqfile1 -2 $seqfile2 -S $readgroup.sam

	#Convert sam file to bam file
	samtools view -F 4 -Sb $readgroup.sam > $readgroup.bam
	
	#Sort and index bam file
	samtools sort $readgroup.bam -o $readgroup.sorted.bam
	samtools index $readgroup.sorted.bam
	
bowtie Heart files include: 

	heart_ERR030886.sample.bam  
	heart_ERR030886.sample.sam 
	heart_ERR030886.sample.sorted.bam 
	heart_ERR030886.sample.sorted.bam.bai

bowtie Kidney files:

	#!/bin/bash
	###############################
	# Resource Manager Directions #
	###############################
	### Set the name of the job
	#PBS -N bowtie_kidney
	### Select the shell you would like to script to execute within
	#PBS -S /bin/bash
	### Inform the scheduler of the expected runtime
	#PBS -l walltime=0:59:00
	### Inform the scheduler of the number of CPU cores for your job
	#PBS -l nodes=1:ppn=4
	### Inform the scheduler of the amount of memory you expect
	#PBS -l mem=2gb
	### Set the destination for your program's output and error
	#PBS -o $HOME/mscbmi/hw/${PBS_JOBNAME}.o${PBS_JOBID}
	#PBS -e $HOME/mscbmi/hw/${PBS_JOBNAME}.e${PBS_JOBID}
	
	#################
	# Job Execution #
	#################
	#Load modules
	module load gcc java-jdk
	module load samtools bowtie2
	
	#Set the file and program paths
	seqPath=~/mscbmi/hw
	outPath=~/mscbmi/hw/bowtie2
	seqfile1=$seqPath/kidney_ERR030885.sample.1.fastq.gz
	seqfile2=$seqPath/kidney_ERR030885.sample.2.fastq.gz
	readgroup=$outPath/kidney_ERR030885.sample
	referenceSeq=/group/referenceFiles/Homo_sapiens/UCSC/hg19/Sequence/IlluminaBowt$
	
	#Output directory exists
	
	#Align pair-ended sequences, 4 threads
	bowtie2 -p 4 -x $referenceSeq -1 $seqfile1 -2 $seqfile2 -S $readgroup.sam

	#Convert sam file to bam file
	samtools view -F 4 -Sb $readgroup.sam > $readgroup.bam
	
	#Sort and index bam file
	samtools sort $readgroup.bam -o $readgroup.sorted.bam
	samtools index $readgroup.sorted.bam

bowtie Kidney files include:

	kidney_ERR030885.sample.bam
	kidney_ERR030885.sample.sam
	kidney_ERR030885.sample.sorted.bam
	kidney_ERR030885.sample.sorted.bam.bai

Make a final shell script that will execute everything above.

	nano finalrun.sh
	
	###################
	#!/bin/bash

	#run raw heart qc
	qsub raw_heart.pbs
	
	#run raw kidney qc
	qsub raw_kidney.pbs
	
	#run bwa on heart files
	qsub bwa_heart.pbs
	
	#run bwa on kidney files
	qsub bwa_kidney.pbs
	
	#run bowtie on heart files
	qsub bowtie_heart.pbs
	
	#run bowtie on kidney files
	qsub bowtie_kidney.pbs

Assign user executing authority and run:

	chmod u+x finalrun.sh
	./finalrun.sh

				
**2) Screen capture the visualization of your mapping results (both bwa and bowtie2 results) for the gene TOMM40L using IGV.**

	cd bwa 
	cd bowtie2

Move heart and kidney bwa AND bowtie2 to local computer on Desktop 
- .sample.sorted.bam  
- .sample.sorted.bam.bai 

Create a shell script to move files to Desktop: 

Open a new terminal and cd Desktop:

	nano desktop.sh

	###################
	#!/bin/bash

	#bwa files to Desktop
	scp t.cri.biowksp16@gardner.cri.uchicago.edu:~/mscbmi/hw/bwa/*.sample.sorted.bam /Users/erikalin/Desktop
	scp t.cri.biowksp16@gardner.cri.uchicago.edu:~/mscbmi/hw/bwa/*.sample.sorted.bam.bai /Users/erikalin/Desktop
	
	#bowtie files to Downloads
	scp t.cri.biowksp16@gardner.cri.uchicago.edu:~/mscbmi/hw/bowtie2/*.sample.sorted.bam /Users/erikalin/Downloads
	scp t.cri.biowksp16@gardner.cri.uchicago.edu:~/mscbmi/hw/bowtie2/*.sample.sorted.bam.bai /Users/erikalin/Downloads
	

#Screenshots in IGV
**Heart**  
bwa:

![](/Users/erikalin/Desktop/heartbwa.png)


bowtie:

![](/Users/erikalin/Desktop/heartbowtie.png)

**Kidney**  
bwa:  

![](/Users/erikalin/Desktop/kidneybwa.png)

bowtie:

![](/Users/erikalin/Desktop/kidneybowtie.png)

