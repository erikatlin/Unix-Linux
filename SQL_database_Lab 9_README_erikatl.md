###Lab 9 - MSBI 32400
####Erika Lin
####March 19, 2019

This lab has several topics:

    - Creating and adding content to tables in SQL
    - Installing pgadmin3
    - Creating an oncokb database and browse tables/data
    - Querying UCSC MySQL 
    - Create a database and import SnpSift extract data from Lab 7
    - Build and populate the database and navigate throughout the database 
    

###Creating a Table in SQL and adding content

    CREATE TABLE variant(id integer primary key,chrom text,start integer,end integer,strand text,name text);
    
    INSERT INTO variant(id, chrom, start, end, strand, name) VALUES(NULL, "16", 48224287, 48224287, "+", "rs17822931");
    
Check "variant" table:

    SELECT COUNT(*) FROM variant;
    SELECT * FROM variant;

###Installing pg3admin on VM 
As student you can run something that requires root command or act as "postgres"
   
    sudo su postgres 
    
Password for student: 4BioInfo!

Check "who you are":

    whoami
    
Change password for database (as postgres) with:

    psql
    \password
    Enter new password: pass
    Enter it again:pass
    \q

Installing pg3admin software command:
    
    sudo yum install pgadmin3
    password: 4BioInfo!
    
In pg3admin: Applications --> Programming --> pgadmin3 

    Set up server groups:
    - msbi32400lab5
    - localhost
    - port: 5432
    - Maintenance DB: postgres
    - Username: postgres
    - Password: pass
    
###Creating an oncokb database

Get file from Canvas: oncokb.dump. Get it to the VM Desktop.

    ls *.dump
    createdb oncokb
    psql oncokb < oncokb.dump
    wc -l oncokb.dump (195413 oncokb.dump)
    
###Browse Data/Tables

Check with command:

    psql oncokb
    oncokb-# \d
    oncokb-# SELECT COUNT(*) FROM alteration
    
    count 
    -------
    13347
    (1 row)

In pgadmin3:
    
    msbi32400 lab --> Databases --> oncokb --> Tables --> "alteration" --> "view data" --> "view top 100 rows" 

With command: 

    psql oncokb
    SELECT id,uuid,entrez_gene_id,alteration,name,alteration_type,consequence, ref_residues,protein_start,protein_end,variant_residues FROM alteration WHERE entrez_gene_id =3845;

- 166 rows were retrieved. 

###Querying UCSC with mysql 

    mysql --user=genome --host=genome-mysql.cse.ucsc.edu -A -e "select chrom, size from hg19.chromInfo" > hg19.genome
    grep "ch" hg19.genome | wc -l
    
- There were 94 lines in the file. 

###Create Lab 9 Folders within data folder with command:

    mkdir -p lab9/data lab9/results lab9/src lab9/doc lab9/bin
    
Copy files from the /data/bds-files/chapter13-out-of-memory directory to data/lab9/data

    cp -pR /data/bds-files/chapter-13-out-of-memory/code-examples/ data/
    
    cp -pR /data/bds-files/chapter-13-out-of-memory/toy-joins/ data/

In: /data/bds-files/chapter-13-out-of-memory/code examples

    python create_table.py
    ls -ltr
    python load_variants.py variants.txt

- There is a variants.txt and variants.db file. A README FILE. A load_variants.py and load-varaints2.py file. 

Use sqlite3 to view variants.db with improved readability: 

    sqlite3 variants.db
    .header on
    .mode column 
    SELECT * FROM variants;
    
  id          chrom       start       end         strand      rsid      
----------  ----------  ----------  ----------  ----------  ----------
1           chr10       114808901   114808902   +           rs12255372
2           chr9        22125502    22125503    +           rs1333049 
3           chr3        46414946    46414978    +           rs333     
4           chr2        136608645   136608646   -           rs4988235 

###Build a table and import SnpSift extracted data from Lab 7 

In /data/lab9/data: Remove first row of extracted data

    cp -p /data/lab7/results/hgvs_test_cases_snpEff.clinvar.Extracted .
    vi hgvs_test_cases_snpEff.clinvar.Extracted
    dd
    :wq


Download from Canvas: create_variants.sql --> sf_Desktop

    psql lab9db < create_variants.sql
    psql lab9db
    lab9db#: \d
    lab9db#: \d variants
    lab9db#: \copy variants FROM 'hgvs_test_cases_snpEff.clinvar.Extracted' WITH DELIMITER E'\t'
    
SELECT COUNT(*) FROM variants;
 count 
-------
   125
(1 row)

    SELECT chr,pos,snp_id, rs,af_exac,ann_gene,ann_effect,ann_impact,ann_feature,ann_hgvs_c,ann_hgvs_p FROM variants WHERE clnsig LIKE '%athogenic' AND ann_gene='BRCA1';
    
 chr |   pos    |    snp_id    |    rs     | af_exac | ann_gene |     ann_effect
      | ann_impact | ann_feature |     ann_hgvs_c     | ann_hgvs_p 
-----+----------+--------------+-----------+---------+----------+---------------
------+------------+-------------+--------------------+------------
 17  | 41197588 | PTV056;96891 | 431825382 | .       | BRCA1    | 3_prime_UTR_va
riant | MODIFIER   | transcript  | c.*103_*106delTGTC | .
(1 row)

    SELECT COUNT(*) FROM variants WHERE clnsig LIKE '%athogenic%';
 
 count 
-------
    29
(1 row)

- Example search results: 
    
    SELECT chr,pos,snp_id, rs,af_exac,ann_gene,ann_effect,ann_impact,ann_feature,ann_hgvs_c,ann_hgvs_p FROM variants WHERE clnsig LIKE '%athogenic';
    
1   |  46655121 | PTV003;56594  | 386834023  | .       | TSPAN1,POMGNT1        
         | downstream_gene_variant,intron_variant                               
                                                                                
| MODIFIER,MODIFIER                | transcript,transcript              
| c.*3917_*3920delTCAC,c.1869+31_1869+34delGTGA          
                                                                                
| .,.

1   | 156108540 | PTV010;66878  | 863225024  | .       | LMNA                  
         | frameshift_variant  
    




  





    
    


