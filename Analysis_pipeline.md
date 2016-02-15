# Analysis Pipeline

## Information about RNAseq data used in this tutorial
**SRA=Sequence Read Archive**  
http://www.ncbi.nlm.nih.gov/sra  
Raw sequencing and alignment data available to research community!  

__Illumina Human Body Map 2.0 Transcriptome Project__    
http://www.ncbi.nlm.nih.gov/Traces/study/  
SRA Study: ERP000546  
Samples: ERR030874, ERR030872, ERR030875, ERR030873, ERR030876, ERR030885, ERR030878, ERR030882, ERR030886, ERR030887, ERR030883, ERR030880, ERR030881, ERR030877, ERR030884, ERR030879.  
  
## Files to download  
  
### Reference files  (Done! Proceed to "working files").  
  
We need a genome against which to align our reads (fasta file):  
Download the reference genome from _UCSC Browser_ (hg19 – GRCh37; Feb 2009 assembly):    
```bash
wget http://hgdownload.cse.ucsc.edu/goldenPath/hg19/chromosomes/chr19.fa.gz  
gunzip chr19.fa.gz  
```  
  
We need a reference gene annotation to quantify expression of known transcripts (.gtf file):  
Download the _human GENCODE Gene Set_:   
GENCODE: http://www.gencodegenes.org/releases/  
```bash
wget ftp://ftp.sanger.ac.uk/pub/gencode/Gencode_human/release_16/gencode.v16.annotation.gtf.gz  
gunzip gencode.v16.annotation.gtf.gz  
grep chr19 gencode.v16.annotation.gtf > gencode.v16.annotation_chr19.gtf  
```  
  
## Working files  
In working directory, download the following files:   
Genome index (used to generate Bowtie2 indexes): **hg19_chr19.fa**  
Transcript annotations: **gencode.v16.annotation_chr19.gtf**  
Source scripts: **source_step1 to step5**  
  
In working directory, create the following directories:  
```bash  
mkdir sources  
mkdir fastq_chr19  
mkdir index_chr19
mkdir gtf_index  
mkdir fpkm_files 
mkdir brain  
```   
In **fastq_chr19/**, save fastq files:  
**brain_end1.fastq.gz**  
**brain_end2.fastq.gz**  

In **fpkm_files/**, save:  
**brain_genes.fpkm_tracking, leukocyte_genes.fpkm_tracking, etc.**
  
In **sources/**, save:   
**source_step1, source_step2, etc.**  
  
In **index_chr19/** and **gtf_index/** Bowtie2 will save genome and transcriptome indexes, respectively.  
  
## Pipeline  
  
>Firstly, test software installation by directly typing their names on prompt.   
If they were correctly added to $PATH, a list of parameters is shown.  
All applications herein used are very well documented, and parameters are explained in **--help command** (e.g., bowtie2 –help) or online manuals.  
Obs.: We can also call the softwares by typing the pathway were executables are installed.  
      e.g, **~/pathway_to_executables/tophat2 -h**    
  
Working directory: **~sources/**  

### Step1: Creating Bowtie2 genome index 
(_source_step1_)  

In this first step, bowtie2 will build an index from a set of DNA sequences in fasta format (the hg19 assembly).  
Bowtie2 is a fast and efficient short read aligner because it uses an econominal data structure (FM index) to store the reference genome sequence, which can be searched rapidly. 

Tophat2 requires the reference **.fa** file in the same directory of Bowtie2 indexes.  
Move **hg19_chr19.fa** to **index_chr19/**.  

```bash
mv ../hg19_chr19.fa ../index_chr19/.
bowtie2-build ../index_chr19/hg19_chr19.fa index_chr19/hg19_chr19  
# Bowtie2 will create six files, which constitute the index:  
# hg19_chr19.1.bt2  
# hg19_chr19.2.bt2  
# hg19_chr19.3.bt2  
# hg19_chr19.4.bt2  
# hg19_chr19.rev.1.bt2  
# hg19_chr19.rev.2.bt2  
```   

### Step2: Creating Bowtie2 transcriptome index   
(_source_step2_)  

In this step, we provide Tophat2 a reference file of known transcripts or junctions (**.gtf**, general transfer format).  
In order to align the reads to known transcripts, Bowtie2 creates an index (also with multiple files) to transcriptome sequences.  
In case multiple Tophat2 runs are planned with the same transcriptome, the first run must include **–G/--GTF** together with **–transcriptome-index** option to create transcriptome index files.  
**–G** is not necessary for subsequent runs.  
  
```bash
tophat2 -G ../gencode.v16.annotation_chr19.gtf --transcriptome-index=../gtf_index/gencode.v16.annotation_chr19 ../index_chr19/hg19_chr19  
```  
  
### Step3: Perform alignment with Tophat2  
(_source_step3_)  

>https://ccb.jhu.edu/software/tophat/tutorial.shtml#ref   
>TopHat map reads firstly by running Bowtie, which identifies places where reads map end to end.
Bowtie will identify “islands” in your reference genome where reads piled up. Many of these islands will be exons. 
TopHat will then find splice junctions using the reads that did not get mapped to an island.  
  
By providing the **--transcriptome-index** option, it will use Bowtie2 to align reads to the virtual transcriptome first (generated in Step2). Reads that do not fully map to the transcriptome will then be mapped on the reference genome and merged with the novel mappings and junctions in the final output.  
  
```bash
tophat2 -p 3 --no-coverage-search --transcriptome-index=../gtf_index/gencode.v16.annotation_chr19 -o ../brain ../index_chr19/hg19_chr19 ../fastq_chr19/brain_end1.fastq.gz ../fastq_chr19/brain_end2.fastq.gz   
```  
> Look for parameters explanation with **tophat2 –-help**.   

TopHat will output a **.bam** file in the output directory **brain/**.  
  
### Step4: Quality control of alignment (SAMtools)   
(_souce_step4_)  

Mapping quality scores quantify the probability that a read is incorrectly aligned, given by **−10 log10 Pr{mapping position is wrong}**,
e.g., 1% error rate would be assigned to a MAPQ=20.  
  
SAM/BAM files contains mapping quality scores and alignments can be filtered through SAMtools **-q** option.  
Select reads with MAPQ>=20 through SAMtools and sort **.bam** files.  
```bash  
mv ../brain/accepted_hits.bam ../brain/brain_chr19.bam  
samtools view -b -q20 ../brain/brain_chr19.bam > ../brain/brain_chr19.q20.bam  
samtools sort  ../brain/brain_chr19.q20.bam ../brain/brain_chr19.q20.sorted  
rm -f ../brain/brain_chr19.q20.bam  
```  
>Load fastQC, open and compare files:   
**brain_end1.fastq.gz X brain_end2.fastq.gz X brain_chr19.q20.sorted**  

### Step5: Estimating gene expression with Cufflinks  
(_source_step5_)  

Alignments are used to accurately quantify gene and transcript expression, as the number of reads produced by a transcript is proportional to its abundance. In this step, Cufflinks assembles mapped reads into transcripts and quantifies expression at transcript  and gene-level.  
  
```bash  
cufflinks -p 3 -G ../gencode.v16.annotation_chr19.gtf -o ../brain  ../brain/brain_chr19.q20.sorted.bam   
```  
Cufflinks will generate several files, including **genes.fpkm_tracking**, which includes gene-level **FPKM** estimations.   
> a. Look at the same files that were generated for several tissues in **fpkm_files/**.  
> b. Understand FPKM estimations (Trapnell et al., 2012).  
> c. Explore data by loading and plotting results in **R**.  

### Intermediate files to download (in case of emergency)  
Bowtie2 indexes and brain_chr19.sorted.q20.bam:
```  
https://dl.dropboxusercontent.com/u/29723062/index_chr19.tar.gz  
https://dl.dropboxusercontent.com/u/29723062/gtf_index.tar.gz  
https://dl.dropboxusercontent.com/u/29723062/brain_chr19.q20.sorted.bam  
   
tar -zxvf gtf_index.tar.gz  
```  
   







