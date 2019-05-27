###Lab 6 - MSBI 32400
####Erika Lin
####February 27, 2019

This lab has several topics:
    
    - Going from FASTQ --> BAM --> VCF with samtools, bwa, bcftools 
    

###Create Lab 6 Folders within data folder with command:
    
    mkdir -p lab6/data lab6/results lab6/src lab6/doc lab6/bin
    
- Inside /data/lab6/data run samtools fastq to extract reads from sample BAM:

    samtools fastq -t /data/bds-files/chapter-11-alignment/NA12891_CEU_sample.bam > NA12891_CEU_sample.fastq


###Download a reference genome from UCSC
From hgdownload.soe.ucsc.edu/goldenPath/hg19/chromosomes/: 
- download chr1.fa.gz 
- move from ~/Downloads to lab6/data folder:

    mv ~/Downloads/chr1.fa.gz  .
    
- gunzip chr1.fa.gz file

    gunzip chr1.fa.gz --> chr1.fa 
    
###Run bwa to build an index for chromosome 1:

    bwa index -a bwtsw chr1.fa
    
###Align and generate SAM file

    bwa mem -R '@RG\tID:MSBI32400_test\tSM:NA12891_CEU_sample' chr1.fa NA12891_CEU_sample.fastq > NA12891_CEU_sample.sam 

###Build index for samtools for chromosome 1:

    time samtools faidx chr1.fa
    
- Convert SAM to BAM

    samtools view -bt chr1.fa.fai NA12891_CEU_sample.sam > NA12891_CEU_sample.bam
    
    samtools sort -o NA12891_CEU_sample_sorted.bam NA12891_CEU_sample.bam
    
    samtools index NA12891_CEU_sample_sorted.bam 

###Generate mpileup and run bcftools (works with VCF conversion)

    samtools mpileup -uf chr1.fa NA12891_CEU_sample_sorted.bam | bcftools call -mv > NA12891_CEU_sample_sorted_var.raw.vcf 
    
     bcftools filter -s LowQual -e '%QUAL<20' NA12891_CEU_sample_sorted_var.raw.vcf > NA12891_CEU_sample_sorted_var.flt.vcf 
     
- How many variants are called in the final VCF? 756

    grep -v "^#" NA12891_CEU_sample_sorted_var.flt.vcf | wc -l
    
- How many variants are called with “PASS”? 700

    grep -v "^#" "PASS" NA12891_CEU_sample_sorted_var.flt.vcf | wc -l
    
###Open BAM/VCF in IGM

    File --> Load from File --> NA12891_CEU_sample_sorted.bam & NA12891_CEU_sample_sorted_var.raw.vcf 

- Find exon 64
Record the coordinates: chr1 215, 844, 373
Amino Acid #: 4692
    
###Samtools 
    
    man samtools

-B: modifies the data which is contained in each alignment.
-C50: The bcftools filter command marks low quality sites and sites where the read depth exceeds a limit. Add -C50 to mpileup if mapping quality is overestimated for reads with excessive mismatches. 





    

  
