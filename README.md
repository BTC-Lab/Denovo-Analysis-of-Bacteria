# De novo Analysis of Bacteria  

A bioinformatics pipeline for performing **De novo genome assembly and analysis of bacteria** from raw sequencing data. This project is designed to assist researchers and microbiologists in reconstructing bacterial genomes without a reference.  
  
The Bioinformatics analysis pipeline for the De novo genome assembly is shown below.   
  
**Briefly perform the following analysis**        
  
  •  	Fastq quality checking and filtering  
  •	  De novo genome assembly  
  •  	Gene prediction  
  •	  Gene annotation  
  
**Technologies & Tools Used**   
  
Python, Perl and Bash Scripting    
AdapterRemoval v2  
Unicycler  
Quast  
Busco  
Prodigal  
Blast  
Fastqc  
  
**Installation**  
  
```
conda create -n Bacterial_ENV  
conda activate Bacterial_ENV  
```
  
**Install dependencies**   
Tools are  available from conda  
Installing using conda: 
  
```  
AdapterRemoval v2  
conda install -c bioconda adapterremoval  
Unicycler  
conda install bioconda::unicycler  
Quast  
conda install bioconda::quast  
Busco  
conda install bioconda::busco  
Prodigal  
conda install bioconda::prodigal  
Blast  
conda install bioconda::blast  
```
  
**Usage**  

**Fastq quality checking and filtering** 
  
This step involves checking of quality parameters for the sequences obtained from sequencer.  
The following checks are performed for an input fastq files.
  
•	Base quality score distributions      
•	Average base content per read   
•	GC distribution in the reads  
  
**Data Pre-Processing**  
  
Before assembly, raw FASTQ files are pre-processed using AdapterRemoval v2.  
  
This step includes:    
   
•	Trimming adapter sequences from paired-end reads      
•	Filtering reads: The pipeline removes any reads where either of the paired-end reads has an average quality score below 30  
    
This ensures that only high-quality, adapter-free reads proceed to the assembly step   
  
**Command:**  
  
Input reads files can be gzipped (or not), and in fastq format  

```  
AdapterRemoval --file1 input_R1.fastq.gz --file2  input_R2.fastq.gz --trimqualities --minquality 30 --minlength 30 --trimwindows 30 --threads <num_threads>  --basename output_Prefix  --output1 output_Preprocessed_R1.fastq.gz --output2 output_Preprocessed_R2.fastq.gz --gzip
```
    
**De novo genome assembly**    
  
Generate the De novo assembly using the unicycler assembler. Assembly statistics can be generated using QUAST and the identification of  levels of conserved genes using BUSCO     
  
**Command:**  
  
Input reads files can be gzipped (or not), and in fastq format  
```  
unicycler -1 input_Preprocessed_R1.fastq.gz  -2  input_Preprocessed_R2.fastq.gz  --keep 0 --threads <num_threads> -o Out
  
quast.py input_Assembly.fasta -o output_Quast -t <num_threads>
  
busco -i input_Assembly.fasta -l bacteria_odb12 -o output_Busco  --mode geno -c <num_threads> 
```
    
**Gene prediction**  
  
Prediction of  CDSs from the unicycler assembled contigs using Prodigal  
**Command**  
  
```  
prodigal -i input_Assembly.fasta -o output_Folder  -a output_Proteins.fasta -d output_Genes.fasta
```    
**Gene annotation**  
  
Briefly, perform the following steps for annotation.  
  
•	Matching with UniProt database using BLASTX program  
•	Organism annotation  
•	Gene Ontology (GO) Annotation    
  
**Matching with Uniprot database using BLASTX program**  
  
Predicted genes are annotated by comparing them against the UniProt database using BLASTX with an E-value threshold of 1e-3. For each gene, the top BLASTX hit is selected based on query coverage, identity, similarity score, and functional description.  

**Using UniProt IDs from a BLAST Search to Retrieve Protein Information**   

After performing a BLAST search on the UniProt website, obtain a list of UniProt accession numbers. These identifiers can be used to access detailed protein information, including protein names, source organisms, and functional annotations.  
  
How It Works 
  
UniProt offers an ID Mapping API that enables:  
  
• Submit a list of UniProt accession IDs  
• Retrieve comprehensive annotation data for each corresponding protein    
   
**Organism annotation**   
   
Each gene’s top BLASTX hit is examined to extract the name of the corresponding organism  

  
**Gene Ontology (GO) Annotation**  
  
The gene ontology (GO) terms (Molecular Function (MF), Cellular Component (CC),Biological Process (BP)) for genes are extracted from the BLASTX hits  
  
  
**Command:**  

```  
diamond blastp --db Uniprot_DB--query input_Proteins.faa --max-target-seqs 1 --outfmt 6 qseqid sseqid pident length mismatch gapopen qstart qend sstart send evalue bitscore qcovhsp stitle --threads <num_threads> --evalue 1e-3 --out output.tab  
```
  

  
  

  
    
    

  



