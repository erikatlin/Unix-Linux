###Lab 4 - MSBI 32400
####Erika Lin
####February 13, 2019

This lab has several topics:

    -Installing a reference proteome on BLAST on VM
    - Running searches in BLAST in a Linux environment
    - Using BLAST in various ways
    - Identifying unknown sequences 

###Retrieving reference data and count proteins

Create a new project directory with command: 

    cd /data/ncbi-blast-2.7.1+/  
    mkdir db
    cd db
    cd /data/ncbi-blast-2.7.1+/db 

From http://uniprot.org/:
"Retrieve the human reference proteome from UniProt (~7MB file)"

Use the command:

    wget ftp://ftp.uniprot.org/pub/databases/uniprot/current_release/knowledgebase/reference_proteomes/Eukaryota/UP000005640_9606.fasta.gz

Extract the downloaded fasta file with Gunzip command:

    gunzip UP000005640_9606.fasta.gz


Grep and pipe fasta file to get word count with command: 

    grep "^>" UP000005640_9606.fasta | wc -l

    Result: 21,074


###Add NCBI binaries to PATH

Locate the BLAST binaries with command:

    cd /data/ncbi-blast-2.7.1+/bin  
    PATH=$PATH:/data/ncbi-blast-2.7.1+/bin
    export PATH
    echo $PATH

###Make a BLAST database

Create a .ncbirc file in home directory with command:

    cd /home/student  
    vi ~/.ncbirc 
    ";Start the section for BLAST configuration
    [BLAST]
    ;Specifies the path where BLAST databases are installed
    BLASTDB=/data/ncbi-blast-2.7.1+/db""

Check work with command: 

    cat ~/.ncbirc

Create BLAST database with command:

    makeblastdb -in UP000005640_9606.fasta -parse_seqids -dbtype prot
    ls -la

    Result: multiple parsed versions of .fasta (i.e. .fasta.psq)

###Test BLAST database with files 

    cd /data
    mkdir -p lab4/data lab4/doc lab4/bin lab4/results lab4/src
    cd lab4/data 

esearch -db protein -query "NP_000509" | efetch -format fasta > hbb.fasta

esearch -db protein -query "NP_976312" | efetch -format fasta > myog.fasta

Downloaded unknown.fasta, unknown_fragment.fasta, unknown2.fasta files from Canvas and moved to HOME folder in VM 
- move from HOME folder to /data/lab4/data folder:

    mv ~/unknown.fasta /data/lab4/data
    mv ~/unknown_fragment.fasta /data/lab4/data
    mv ~/unknown2.fasta /data/lab4/data

###Running blastp and BLAST proteins
In the directory: cd /data/ncbi-blast-2.7.1+/bin

    blastp -h

In the directory: cd/data/lab4/data

    blastp -query hbb.fasta-db/data/ncbi-blast-2.7.1+/db/UP000005640_9606.fasta

View in Chrome by creating html: 

    blastp- query hbb.fasta-db/data/ncbi-blast-2.7.1+/db/UP000005640_9606.fasta -out mysearch.html–html

    cp mysearch.html /media/sf_Desktop/

Open mysearch.html from Desktop in Chrome

    E-Value result of top hit: Hemoglobin subunit beta = 4e-107 (score = 301 bits)

### Identifying unknown sequences and mismatches
Run Blastp with unknown.fasta:

Use command:
    
    blastp -query unknown.fasta-db/data/ncbi-blast-2.7.1+/db/UP000005640_9606.fasta

    E-Value result of top hit: Hemoglobin subunit alpha = 1e-101 (score = 286 bits)
    
Run Blastp with unknown2.fasta:

Use command:

    blastp -query unknown2.fasta-db/data/ncbi-blast-2.7.1+/db/UP000005640_9606.fasta

    E-value result of top hit: Hemoglobin subunit beta = 3e-106 (score =298 bits)
    The first hit, hemoglobin subunit beta, did not match all amino acids. Query     1 shows a gap in its sequence in comparison to the reference (Subject 1)         sequence. The base in the query is a V, while in the reference it is a E. The     potential mismatch is in position 6. 
    

###Run blastp with unknown_fragment.fasta

Check if protein/nucleotide:

Use command in /data/lab4/data:

    head unknown_fragment

- unknown_fragment.fasta is a protein
    
Use command:

    blastp -query unknown_fragment.fasta-db/data/ncbi-blast-2.7.1+/db/UP000005640_9606.fasta
    
    E-value result of top hit: Breast cancer type 1 susceptibility protein = 0.0 (score = 2307 bits)







