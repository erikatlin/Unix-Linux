###Final Project - MSBI 32400
####Erika Lin
####March 22, 2019

This lab has several topics:
    
    - Call variants on Pevsner autism BAM 
    - Annotate Pevsner autism call variants with snpEff & Clinvar
    - Upload (unannotated) VCF to VEP for gnomAD
    - Compare variants in the 101 target gene list with gnomAD frequencies for expected autism frequencies from CDC (most recent)
    
###Create Final Project Folders within data folder with command:

     mkdir -p final/data final/results final/src final/doc final/bin
     
In data/project/data: move downloaded files from Desktop

    mv /media/sf_Desktop/twoBitToFa .
    mv /media/sf_Desktop/hg19.2bit .
    mv /media/sf_Desktop/Pevsner_9-7_mysample1.bam .
    
###Change twoBitToFa to execute

    chmod +x twoBitToFa
    echo $PATH
    mv twoBitToFa /home/student/edirect
    
###Extract the hg19.fa
    
    twoBitToFa hg19.2bit hg19.fa

###Index hg19.fa 

    samtools faidx hg19.fa
  
- A .fai file (indexed components of hg19.fa) was generated that was smaller than the .fa file. 
###Run samtools mpilep using hg.19.fa as the reference 

    samtools mpileup -uf hg19.fa Pevsner_9-7_mysample1.bam | bcftools call -mv > Pevsner_9-7_mysample1.var.raw.vcf
    ls -lth
    Pevsner_9-7_mysample1.raw.vcf

###SnpEff and Clinvar on the Pevsner_9-7_mysample1.var.raw.vcf. Put the final vcf into /data/final/results.

    java -Xmx2G -jar /data/snpEff/snpEff.jar eff -canon -noLog hg19 Pevsner_9-7_mysample1.var.raw.vcf > /data/final/results/Pevsner_final.vcf

    java -Xmx2G -jar /data/snpEff/SnpSift.jar annotate -noLog /data/snpEff/data/hg19/clinvar/clinvar_201902011.vcf.gz /data/final/results/Pevsner_final.vcf > /data/final/results/Pevsner_final.clinvar.vcf
    
![](/Users/erikalin/Documents/Health Informatics/UChicago/Introduction to Bioinformatics/Homework /final pictures/terminal.png)
    
###Access: http://grch37.ensembl.org/Homo_sapiens/Tools/VEP/ 
- Upload Pevsner_9-7_mysample1.var.raw.vcf to VEP with filters on VEP 

    RefSeq transcripts
    Gene Symbol 
    HGVS
    gnomAD (exomes) allele frequencies
    PubMed terms
    Restrict: show one consequence per variant 

From: https://www.cdc.gov/ncbddd/autism/data.html
- From Surveillance year (2014) and birth year (2006) --> 1 in 59 children have Autism (1/59 ~ 0.017)

In Results:
- Set filter:
    
    gnomAD AF < 0.017
    Consequence is not 'synonymous variant'
    Impact is 'high'
    Impact is 'moderate'

 ~50 results generated 

- Compare with Pevsner 101 gene list for possible Autism genes

    FOLR1 (high impact)
    - consequence: splice donor
    - biotype: protein coding
    - gnomAD probability is 0.0034
    
    ZEB2 (moderate impact)
    - consequence: missense
    - biotype: protein coding
    - gnomAD probability is 0.0002
    
    SCN1A (moderate impact)
    - consequence: missense
    - biotype: protein coding
    - gnomAD probability is 0.0003
    
    AVPR1A (moderate impact)
    - consequence: missense
    - biotype: protein coding
    - gnomAD probability is 0.0128
    
    PNKP (moderate impact)
    - consequence: missense
    - protein coding
    - gnomAD probability is 0.0025
    
    

   
