###Lab 8 - MSBI 32400
####Erika Lin
####March 13, 2019

This lab has several topics:

    - DEMO: Working with Tumor Normal Data
    - DEMO: Annotating tumor/normal VCF
    - Annotating gene panel data
    - Using IGV to view cancer patient data, gene lists, networks, etc...
    - DEMO: Vignette to determine how many genes 
  
###Create Lab 8 Folders within data folder with command:

    mkdir -p lab8/data lab8/results lab8/src lab8/doc lab8/bin
    

###Viewed Cancer Hotspot data .bam, .bed, .vcf in IGV 
Data found in: 
/data/variantCaller_out.170/IonXpress_003

###Annotate data to find regions of interests with snpEff and Clinvar
cd /data/lab/8/results: 

    java -Xmx2G -jar /data/snpEff/snpEff.jar eff -canon -noLog hg19 /data/variantCaller_out.170/IonXpress_003/TSVC_variants.vcf > TSVC_variants.snpEff.vcf
    
    java -Xmx2G -jar /data/snpEff/SnpSift.jar annotate -noLog /data/snpEff/data/hg19/clinvar_20190211.vcf.gz TSVC_variants.snpEff.vcf > TSVC_variants.snpEff.clinvar.vcf
    
###Filter data
Show SNPs that aren't absent: 

    grep -v "^#" TSVC_variants.snpEff.clinvar.vcf | grep -v '0/0'

Show stop variants: 
    
    grep -v "^#" TSVC_variants.snpEff.clinvar.vcf | grep -v '0/0' | grep stop

###Adjust IGV allele frequency 
In IGV header:

View --> Preferences --> Alignments tab --> change allele frequency to 0.05 

###Select STOP variant AA/CDS mutation and find on COSMIC database (TP53)

c.511G>C|p.Glu171Gln:

    Coordinates: 17:7669609..7676594  (negative strand) 
    
###Filter IGV view with gene list 

Regions --> Gene Lists --> p53 signaling --> View (TP53)


###EGFR Hotspot Sample Inspection
In cd /data:

    chmod 775 EGFR_19_DEL_EGFR_20_INS.143

- Open EGFR BAM, BCF, and bed file and inspect Exon 19 in IGV 

    Observe the 15 base deletion. 
    Expand the hotspots --> COSMIC ID: COSM24869
    Genomic coordinates: GRCH38, 7:55174747..55174814 (69 b)
    Commonly seen in lung cancer (adenocarcinomas)

- In KEGG gene list find 2 other SNPs from different genes

     TP53: G-->C SNP, chromosome 17:7,579, 472, exon #4, (COSM 45985)
     KRAS: G--> A SNP, chromosome 12: 25, 380, 261, exon #3 (COSM 12729/87298)










