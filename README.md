# Translating metabolic pathway functions from dysbiosis data
The gut microbiome affects human health via bacterial production to metabolic pathways. The goal of this project is to understand how multiple members of a bacterial community contribute to metabolic pathways. We illustrate our results by using a computational tool to predict bacterial metabolite contributions in 16S and metagenomic data for Crohn's Disease patients.

## Installation requirements:

### Install qiime2 requirements
Qiime2 version 2021.8 was used for this project. For installation instructions, see documentation on qiime's website: https://docs.qiime2.org/2021.11/install/native/#install-qiime-2-within-a-conda-environment

### Data access
16S and metagenomic data are available on SRA with accession number SRP114403: https://www.ncbi.nlm.nih.gov/sra/?term=SRP114403.

```
mkdir data
cd data
curl -LJO "https://sra-downloadb.be-md.ncbi.nlm.nih.gov/sos2/sra-pub-run-11/SRR5883619/SRR5883619.2"
curl -LJO "https://sra-downloadb.be-md.ncbi.nlm.nih.gov/sos2/sra-pub-run-11/SRR5883620/SRR5883620.2"
curl -LJO "https://sra-downloadb.be-md.ncbi.nlm.nih.gov/sos2/sra-pub-run-11/SRR5883621/SRR5883621.1"
curl -LJO "https://sra-downloadb.be-md.ncbi.nlm.nih.gov/sos2/sra-pub-run-11/SRR5883622/SRR5883622.1"
curl -LJO "https://sra-downloadb.be-md.ncbi.nlm.nih.gov/sos2/sra-pub-run-11/SRR5883623/SRR5883623.2"
curl -LJO "https://sra-downloadb.be-md.ncbi.nlm.nih.gov/sos2/sra-pub-run-11/SRR5883624/SRR5883624.2"
curl -LJO "https://sra-downloadb.be-md.ncbi.nlm.nih.gov/sos2/sra-pub-run-11/SRR5883625/SRR5883625.2"
curl -LJO "https://sra-downloadb.be-md.ncbi.nlm.nih.gov/sos2/sra-pub-run-11/SRR5883626/SRR5883626.2"
curl -LJO "https://sra-downloadb.be-md.ncbi.nlm.nih.gov/sos2/sra-pub-run-11/SRR5883627/SRR5883627.2"
curl -LJO "https://sra-downloadb.be-md.ncbi.nlm.nih.gov/sos2/sra-pub-run-11/SRR5883628/SRR5883628.2"
curl -LJO "https://sra-downloadb.be-md.ncbi.nlm.nih.gov/sos2/sra-pub-run-11/SRR5883629/SRR5883629.2"
curl -LJO "https://sra-downloadb.be-md.ncbi.nlm.nih.gov/sos2/sra-pub-run-11/SRR5883630/SRR5883630.2"
```

The reads were converted to FASTQ formatting using the SRA Toolkit. Installation instructions for SRA Toolkit can be found here: http://www.sthda.com/english/wiki/install-sra-toolkit

```
fastq-dump --outdir SRR5883619.2-split --split-files --origfmt SRR5883619.2
fastq-dump --outdir SRR5883620.2-split --split-files --origfmt SRR5883620.2
fastq-dump --outdir SRR5883621.1-split --split-files --origfmt SRR5883621.1
fastq-dump --outdir SRR5883622.1-split --split-files --origfmt SRR5883622.1
fastq-dump --outdir SRR5883623.2-split --split-files --origfmt SRR5883623.2
fastq-dump --outdir SRR5883624.2-split --split-files --origfmt SRR5883624.2
fastq-dump --outdir SRR5883625.2-split --split-files --origfmt SRR5883625.2
fastq-dump --outdir SRR5883626.2-split --split-files --origfmt SRR5883626.2
fastq-dump --outdir SRR5883627.2-split --split-files --origfmt SRR5883627.2
fastq-dump --outdir SRR5883628.2-split --split-files --origfmt SRR5883628.2
fastq-dump --outdir SRR5883629.2-split --split-files --origfmt SRR5883629.2
fastq-dump --outdir SRR5883630.2-split --split-files --origfmt SRR5883630.2
```

When done, move back to your base directory.
```
cd ..
```

## 16S Data analysis
16S data analysis was carried out using Qiime2 version 2021.11. The paired-end sequence reads were imported and processed using a manifest file.

Paste the following contents into a new file named "manifest.tsv".
```
sample-id	forward-absolute-filepath	reverse-absolute-filepath
S8_16S	$WORKING_DIR/data/SRR5883619.2-split/SRR5883619.2_1.fastq	$WORKING_DIR/data/SRR5883619.2-split/SRR5883619.2_2.fastq
S9_16S	$WORKING_DIR/data/SRR5883620.2-split/SRR5883620.2_1.fastq	$WORKING_DIR/data/SRR5883620.2-split/SRR5883620.2_2.fastq
S6_16S	$WORKING_DIR/data/SRR5883621.1-split/SRR5883621.1_1.fastq	$WORKING_DIR/data/SRR5883621.1-split/SRR5883621.1_2.fastq
S7_16S	$WORKING_DIR/data/SRR5883622.1-split/SRR5883622.1_1.fastq	$WORKING_DIR/data/SRR5883622.1-split/SRR5883622.1_2.fastq
S1_16S	$WORKING_DIR/data/SRR5883623.2-split/SRR5883623.2_1.fastq	$WORKING_DIR/data/SRR5883623.2-split/SRR5883623.2_2.fastq
S3_16S	$WORKING_DIR/data/SRR5883624.2-split/SRR5883624.2_1.fastq	$WORKING_DIR/data/SRR5883624.2-split/SRR5883624.2_2.fastq
S8_Shotgun	$WORKING_DIR/data/SRR5883625.2-split/SRR5883625.2_1.fastq	$WORKING_DIR/data/SRR5883625.2-split/SRR5883625.2_2.fastq
S9_Shotgun	$WORKING_DIR/data/SRR5883626.2-split/SRR5883626.2_1.fastq	$WORKING_DIR/data/SRR5883626.2-split/SRR5883626.2_2.fastq
S6_Shotgun	$WORKING_DIR/data/SRR5883627.2-split/SRR5883627.2_1.fastq	$WORKING_DIR/data/SRR5883627.2-split/SRR5883627.2_2.fastq
S7_Shotgun	$WORKING_DIR/data/SRR5883628.2-split/SRR5883628.2_1.fastq	$WORKING_DIR/data/SRR5883628.2-split/SRR5883628.2_2.fastq
S1_Shotgun	$WORKING_DIR/data/SRR5883629.2-split/SRR5883629.2_1.fastq	$WORKING_DIR/data/SRR5883629.2-split/SRR5883629.2_2.fastq
S3_Shotgun	$WORKING_DIR/data/SRR5883630.2-split/SRR5883630.2_1.fastq	$WORKING_DIR/data/SRR5883630.2-split/SRR5883630.2_2.fastq
```

Data analysis was carried out using publicly available code on GitHub: https://gitlab.com/JoanML/colonbiome-pilot/-/tree/master/. This step requires cloning the publicly available repository using Git. For Git installation instructions, see the following link: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git 

```
git clone https://gitlab.com/JoanML/colonbiome-pilot.git
```
