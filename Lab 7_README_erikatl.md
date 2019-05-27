###Lab 7 - MSBI 32400
####Erika Lin
####March 6, 2019

This lab has several topics:
    
    - Download Yen's test VCF from Github
    - Run snpEFF against hg19 and Clinvar 
    - Upload an unannotated version of Yen's test VCF --> VEP, wANNOVAR, and SeattleSeq 
    - Using SnpSift to extract certain VCF data 
    - Compare online results with Yen's results (VCF, snpEff, SnpSift)
    
###Create Lab 7 Folders within data folder with command:

    mkdir -p lab7/data lab7/results lab7/src lab7/doc lab7/bin


###Download Yen's test data set from Github 
From pubmed.gov: search for PMID: 28122645
- select "RAW" and save the file 
- move the file from ~/Downloads to lab7/data folder with command:

    mv ~/Downloads/hgv_test_cases  .
    
###Analyze with snpEff + Clinvar 
SnpEff:
    
    java -Xmx2G -jar /data/snpEff/snpEff.jar eff -canon -noLog hg19 hgvs_test_cases.vcf > /data/lab7/results/hgvs_test_cases_snpEff.vcf

Clinvar: 

    java -Xmx2G -jar /data/snpEff/SnpSift.jar annotate -noLog /data/snpEff/data/hg19/clinvar/clinvar_20190211.vcf.gz /data/lab7/results/hgvs_test_cases_snpEff.vcf > /data/lab7/results/hgvs_test_cases_snpEff.clinvar.vcf
    
###Extract data from VCF with SnpSift (annotated)

    java -Xmx2G -jar /data/snpEff/SnpSift.jar extractFields -s ',' -e '.' /data/lab7/results/hgvs_test_cases_snpEff.clinvar.vcf CHROM POS REF ALT ID "ANN[*].ALLELE" "ANN[*].EFFECT" "ANN[*].IMPACT" "ANN[*].GENE" "ANN[*].FEATURE" "ANN[*].FEATUREID" "ANN[*].BIOTYPE" "ANN[*].RANK" "ANN[*].HGVS_C" "ANN[*].HGVS_P" "ANN[*].CDNA_POS" "ANN[*].CDNA_LEN" "ANN[*].AA_LEN" "ANN[*].DISTANCE" "LOF[*].GENE" "LOF[*].NUMTR" "LOF[*].PERC" CLNREVSTAT RS CLNDNINCL ORIGIN MC CLNDN CLNVC CLNVI AF_EXAC AF_ESP CLNSIG CLNSIGINCL CLNDISDB GENEINFO CLNDISDBINCL AF_TGP CLNHGVS SSR > /data/lab7/results/hgvs_test_cases_snpEff.clinvar.Extracted


###Upload test VCF to online sites
    
    wANNOVAR:
    - chromosome 11 (POS: 62457852)
      - (blank)
    - chromosome 14 (POS: 62187287)
      - (blank)
    
    VEP:
    - chromosome 11 (ID:PTV015)
    - chromosome 14 (ID:PTV027)
    
    SeattleSeq: 
    - chromosome 11 (POS: 62457852)
    - chromosome 14 (POS: 62187287)

###Compare results (choose 2 SNPs on different chromsomes from VCF, local snpEFF, SnpShift)
In /data/lab7/results:


    SnpSift: I feel like this file is corrupted....
    - chromosome 11 (POS:62457852)
      - c.1376G>T
      - p.Cys459Phe
    
    - chromosome 14 (POS:62187287)
      - c.295G>A	
      - p.Ala99Thr
    

Some databases (i.e. wANNOVAR) did not have the same c./p. readings as the extracted VCF. 





