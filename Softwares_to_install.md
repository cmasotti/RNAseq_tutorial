# Softwares to download, install, and add to $PATH  

> How to add the applications to $PATH (options):  
1. Directly write in .bashrc file:  
```bash
export PATH=PATH:/pathway/to/software_directory  
then: source ~/.bashrc  
``` 
2. Copy executable files from software_directory/bin to /user/local/bin  
3. Create symbolic links (ln –s) to executables in /user/local/bin  

## BEDtools (version 2.25.0) (Quinlan and Hall, 2010)  
http://bedtools.readthedocs.org  
Download: https://github.com/arq5x/bedtools2/releases  
```bash
wget https://github.com/arq5x/bedtools2/releases/download/v2.25.0/bedtools-2.25.0.tar.gzcd bedtools-2.25.0  
tar zxvf bedtools-2.25.0.tar.gz  
cd bedtools2/  
make  
cp bin/* usr/local/bin/.  
```

## SAMtools (version 0.1.18) (Li et al., 2009)   
http://sourceforge.net/projects/samtools/files/samtools/0.1.18/  
```bash
wget http://sourceforge.net/projects/samtools/files/samtools/0.1.18/samtools-0.1.18.tar.bz2  
tar zxvf samtools-0.1.18.tar.bz2  
cd samtools-0.1.18  
make  
export PATH=PATH:/pathway/to/samtools-0.1.18  
source ~/.bashrc  
````
  
## FastQC  
http://www.bioinformatics.babraham.ac.uk/projects/fastqc/  
Requirement: suitable Java Runtime Environment  
```bash
wget http://www.bioinformatics.babraham.ac.uk/projects/fastqc/fastqc_v0.11.4.zip  
````
  
## Bowtie 2.1.0 (Langmead et al., 2009)   
http://sourceforge.net/projects/bowtie-bio/files/bowtie2/2.1.0/  
```bash
wget http://sourceforge.net/projects/bowtie-bio/files/bowtie2/2.1.0/bowtie2-2.1.0-linux-i386.zip  
unzip bowtie2-2.1.0-linux-i386.zip  
cd bowtie2-2.1.0  
```  
Copy all executables below to  ~/usr/local/bin/.  
bowtie2  
bowtie2-align  
bowtie2-align-debug  
bowtie2-build  
bowtie2-build-debug  
bowtie2-inspect  
bowtie2-inspect-debug  
  
## TopHat (version 2.0.3) (Trapnell et al., 2009) 
http://ccb.jhu.edu/software/tophat/downloads/  
```bash
wget http://ccb.jhu.edu/software/tophat/downloads/tophat-2.0.3.0.Linux_x86_64.tar.gz  
tar zxvf tophat-2.0.3.0.Linux_x86_64.tar.gz  
cd ~/usr/local/bin  
ln –s ~/tophat-2.0.3.0.Linux_x86_64/tophat2 .  
````

## Cufflinks (version 2.2.1) (Trapnell et al., 2012)  
http://cole-trapnell-lab.github.io/cufflinks/manual/  
```bash  
wget http://cole-trapnell-lab.github.io/cufflinks/assets/downloads/cufflinks-2.2.1.Linux_x86_64.tar.gz  
tar zxvf  cufflinks-2.2.1.Linux_x86_64.tar.gz  
cd cufflinks-2.2.1.Linux_x86_64  
```  
Copy all executables below to  ~/usr/local/bin/.  
cuffcompare  
cuffdiff  
cufflinks  
cuffmerge  
cuffnorm  
cuffquant  
gtf_to_sam  
gffread  

## R packages  
Open R session to install packages:  
```R  
# ggplot2: https://cran.r-project.org/web/packages/ggplot2/ggplot2.pdf  
install.packages(“ggplot2”)  
install.packages(“grid”)  
install.packages(“gridExtra”)  
  
# Plyr: https://cran.r-project.org/web/packages/plyr/plyr.pdf  
install.packages(“plyr”)  
  
# Reshape: https://cran.r-project.org/web/packages/reshape/reshape.pdf  
install.packages(“reshape”)  

# CummRbund: http://bioconductor.org/packages/release/bioc/html/cummeRbund.html  
# Requirement: Bioconductor release 3.2   
source("https://bioconductor.org/biocLite.R")   
biocLite("cummeRbund")  
````

## References  
  
Langmead, B., Trapnell, C., Pop, M., and Salzberg, S.L. (2009). Ultrafast and memory-efficient alignment of short DNA sequences to the human genome. Genome Biol. 10, R25.  

Li, H., Handsaker, B., Wysoker, A., Fennell, T., Ruan, J., Homer, N., Marth, G., Abecasis, G., Durbin, R., and Subgroup, 1000 Genome Project Data Processing (2009). The Sequence Alignment/Map format and SAMtools. Bioinformatics 25, 2078–2079.  
  
Quinlan, A.R., and Hall, I.M. (2010). BEDTools: A flexible suite of utilities for comparing genomic features. Bioinformatics 26, 841–842.  
  
Trapnell, C., Pachter, L., and Salzberg, S.L. (2009). TopHat: Discovering splice junctions with RNA-Seq. Bioinformatics 25, 1105–1111.  
  
Trapnell, C., Roberts, A., Goff, L., Pertea, G., Kim, D., Kelley, D.R., Pimentel, H., Salzberg, S.L., Rinn, J.L., and Pachter, L. (2012). Differential gene and transcript expression analysis of RNA-seq experiments with TopHat and Cufflinks. Nat. Protoc. 7, 562–578.  





