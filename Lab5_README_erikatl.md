###Lab 5 - MSBI 32400
####Erika Lin
####February 20, 2019

This lab has several topics:

    - Download some public 23andMe data from the VM to explore SNPs
    - Download software from Github to convert file to VCF
    - Analyze the converted VCF file
    - Annotate the SNPs
    - Changing memory on the VM 
    - Annotating using RefSeq & generating/reviewing snpEFF summaries
    - Using and annotating with SnpSift 
    - Accessing and reviewing PharmGKB TPMT translation table and identifying "star allele" status
    - Uploading/analyzing 23andMe data on GENOtation 
    
###Download 23andMe data from: https://opensnp.org/ and search for AuriCrow within 'genotypes'
- renamed downloaded file to AuriCrow.6002 

###Create a new lab 5 folder with command:

    mkdir -p lab5/data lab5/results lab5/src lab5/doc lab5/bin
    
###Move AuriCrow.6002 from downloads to lab5/data folder with command:

    mv ~/Downloads/AuriCrow.6002 /data/lab5/data
    
###Inspect the genotype file 

    cd /data/lab5/data
    grep "^#" AuriCrow.6002 | wc -l
    
The columns of information was: rsid, chromosome, position, genotype 

###Download Github software to convert the downloaded file to VCF

    cd /data/lab5/bin 
    git clone https://github.com/arrogantrobot/23andme2vcf.git
    cd /data/lab5/bin/23andme2vcf
    
###Run Perl script to convert 23andMe file to VCF:
- Inside the data/lab5/bin/23andme2vcf folder run command:

    perl 23andme2vcf.pl /data/lab5/data/AuriCrow.6002 
    /data/lab5/results/AuriCrow.6002.vcf5
    
    cd /data/lab5/results 
    grep -v "^#" AuriCrow.6002.vcf | wc -l

How many lines were excluded in the conversion? No lines were excluded

###Annotating SNPs 
- Access Java application, snpEff, on the VM:
  
    java -Xmx2G -jar /data/snpEff/snpEff.jar
    
- Memory was changed last week in VM to 4096 MB
- In lab/results:
  
    java -Xmx2G -jar /data/snpEff/snpEff.jareff-canon -noLoghg19 AuriCrow.6002.vcf > AuriCrow.6002.snpEff.vcf
- took ~1 min for my VM to process

###Reading annotations from snpEff

    less AuriCrow.6002.snpEff.vcf
    /stop
    
- Hit "n" to repeat the search again

chr1    11906068        rs5065  A       G       .       .       ANN=G|stop_lost|HIGH|
(chromosome 1, position, stop lost, high impact)

chr1    158549492       rs863362        C       T       .       .  ANN=T|stop_gained|HIGH|
(chromosome 1, position, stop gained, high impact)

chr2    73927931        i6053792        A       G       .       .       ANN=G|stop_lost|HIGH|
(chromosome 2, position, stop lost, high impact)

###Opened the snpEff_summary.html in Desktop of actual computer
- How many were classified as “stop_lost” and how many as “stop_gained”?
    
    stop_lost = 7
    stop_gained = 14

###Use Clinvar 
- Use SnpSift, companion package of snpEff, to add anonotations to the existing AuriCrow.6002.snpEff.vcf file 
- Open Firefox --> on the NCBI website select Clinvar --> go to Clinvar FTP site --> download:

    GRCh37 (aka Hg19 in NCBI)
      clinvar_20190211.vcf.gz
      clinvar_20190211.vcf.gz.tbi

- Create and move both files to /data/snpEff/data/hg19/clinvar/ 

    cd /data/lab5
    cd /data/snpEff/data/hg19
    mkdir clinvar
    cd clinvar/
    pwd
    cd /data/snpEff/data/hg19/clinvar 
    
- Find files in downloads and move from downloads to current clinvar folder

    ls -ltr ~/Downloads/ 
    mv ~/Downloads/clinvar_20190211.vcf.gz* . (moves .gz and .tbi files to Downloads)
    ls -la 

###Annotate snpEff.vcf with SnpSift

    java -Xmx2G -jar /data/snpEff/SnpSift.jar annotate -noLog        /data/snpEff/data/hg19/clinvar/clinvar_20190211.vcf.gz /data/lab5/results/AuriCrow.6002.snpEff.vcf > /data/lab5/results/AuriCrow.6002.clinvar.snpEff.vcf

- Inspect the new VCF with SnpSift
    
    less AuriCrow.6002.clinvar.snpEff.vcf
    search by: /CLNSIG=Pathogenic 

chr8    11606312        rs3735819;433016  (CLNDN=Congenital_heart_disease)

chr16   50756774        rs5743289;4697  (CLNDN=Inflammatory_bowel_disease_1|Blau_syndrome|Yao_syndrome)

###Pharmacogenomics Exercise
On this website: http://www.pharmgkb.org
- Search TPMT (gene)
- Haplotypes --> download the translation table for TPMT

Look for rsIDS: rs1800462, rs1142345, rs1800460 & rs1800584
- Determine the “star allele” status for TPMT from the 23andMe data using the translation table given 

    cd /data/lab5/data
    /rs1800462, rs1142345, rs1800460, rs1800584

rs1800462 CC
rs1142345 TT
rs1800460 CC
rs1800584 CC
    
    The "star allele" status would be *1/*1 (CTCC/CTCC)
    
###Interpreting SNP Data online 
On this website: http://genotation.stanford.edu/
- Upload AuriCrow.6002 data 
- Population: European 

What is their interpretation of TPMT status?

    Patients with the CC genotype may have a decreased risk for toxicity with thiopurine/purine analogue drugs compared patients with the CT/TT genotype. 

#Describe the Clinical/Lung Function results.
   
    Aurie Crow carries a risk allele for obstructive diseases. However, not all genes are associated with obstructive disease. For rs7068966 associated with FEV1, the beta score of .03 means that each T allele raises the FEV1 volume by .03 mls. Therefore, Aurie Crow has a greater FEV1 volume. 
    
    
What is your CYP2C19 genotype and how does it affect function?

    Patients with the CC genotype: 
    1) may have decreased activation of clopidogrel 
    2) may have a decreased risk for bleeding with clopidogrel as compared to patients with the CT/TT genotype 
    3) may have an increased risk for adverse cardiovascular events as compared to patients with a CT/TT genotype.

Does your genotype have any risk alleles associated with Caffeine consumption?

    The genotype indicates that Auri Crow does not have any risk alleles for Caffeine consumption.

Does your genotype have any risk for motion sickness?

    It is suspected that Auri Crow is at risk for motion sickness since she holds some risk alleles. However, not all genes are associated with motion sickness. For instance, her motion sickness has a higher risk associated with neurological development (rs34912216/GG/risk allele:G) 
    
Compare some predicted traits with AuriCrow’s self-reported traits.

Eye Color: 

    - Prediction: In HERC2, the well-known SNP rs12913832 explains 56.1% of the variance in eye color. rs12913832, rs1667394 and rs12896399 are also associated with green versus blue eyes.
    - Self-reported: Blue, grey, green, changing
    

  



