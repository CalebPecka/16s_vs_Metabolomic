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
fastq-dump --outdir SRR5883625.2-fastq --origfmt SRR5883625.2
fastq-dump --outdir SRR5883626.2-fastq --origfmt SRR5883626.2
fastq-dump --outdir SRR5883627.2-fastq --origfmt SRR5883627.2
fastq-dump --outdir SRR5883628.2-fastq --origfmt SRR5883628.2
fastq-dump --outdir SRR5883629.2-fastq --origfmt SRR5883629.2
fastq-dump --outdir SRR5883630.2-fastq --origfmt SRR5883630.2
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
```

And now we can run Qiime2 commands within your conda environment.
```
qiime tools import --type 'SampleData[PairedEndSequencesWithQuality]' --input-path manifest.tsv --output-path demux.qza --input-format PairedEndFastqManifestPhred33V2
```

Preprocessing parameters were followed based on the original paper: https://doi.org/10.1089/omi.2018.0013
```
qiime dada2 denoise-paired --i-demultiplexed-seqs demux.qza --p-trim-left-f 17 --p-trim-left-r 21 --p-trunc-len-f 250 --p-trunc-len-r 250 --o-representative-sequences rep-seqs-demux.qza --o-table table.qza --o-denoising-stats stats-data2.qza
```

Download reference classifier databases. 
```
curl -LJO https://data.qiime2.org/2021.11/common/silva-138-99-nb-classifier.qza
curl -LJO https://data.qiime2.org/2021.11/common/gg-13-8-99-nb-classifier.qza
curl -LJO https://data.gtdb.ecogenomic.org/releases/release202/202.0/bac120_taxonomy_r202.tsv.gz
curl -LJO https://data.gtdb.ecogenomic.org/releases/release202/202.0/genomic_files_reps/bac120_ssu_reps_r202.tar.gz
gunzip bac120_taxonomy_r202.tsv
tar -zxvf bac120_ssu_reps_r202.tar.gz
rm -rf bac120_ssu_reps_r202.tar.gz
qiime tools import --type 'FeatureData[Taxonomy]' --input-format HeaderlessTSVTaxonomyFormat --input-path bac120_taxonomy_r202.tsv --output-path gtdb-base-taxonomy.qza
qiime tools import --type 'FeatureData[Sequence]' --input-path bac120_ssu_reps_r202.fna --output-path sequences.qza
qiime feature-classifier fit-classifier-naive-bayes --i-reference-reads sequences.qza --i-reference-taxonomy gtdb-base-taxonomy.qza --o-classifier gtdb-classifier.qza

qiime feature-classifier classify-sklearn --i-classifier silva-138-99-nb-classifier.qza --i-reads rep-seqs-demux.qza --o-classification silva-taxonomy.qza
qiime feature-classifier classify-sklearn --i-classifier gg-13-8-99-nb-classifier.qza --i-reads rep-seqs-demux.qza --o-classification gg-taxaonomy.qza
qiime feature-classifier classify-sklearn --i-classifier gtdb-classifier.qza --i-reads rep-seqs-demux.qza --o-classification gtdb-taxonomy.qza
```

Paste the following contents into a new file called "metadata.tsv".
```
sample-id diagnosis
S8_16S  FGID
S9_16S  CD
S6_16S  FGID
S7_16S  FGID
S1_16S  CD
S3_16S  CD
```

Create a heatmap. This requires collapsing taxonomic ID's into the table files.
```
qiime taxa collapse --i-table table.qza --i-taxonomy silva-taxonomy.qza --p-level 7 --o-collapsed-table silva-table.qza
qiime taxa collapse --i-table table.qza --i-taxonomy gg-taxonomy.qza --p-level 7 --o-collapsed-table gg-table.qza
qiime taxa collapse --i-table table.qza --i-taxonomy gtdb-taxonomy.qza --p-level 7 --o-collapsed-table gtdb-table.qza

qiime feature-table heatmap --i-table silva-table.qza --m-sample-metadata-file metadata.tsv --m-sample-metadata-column diagnosis --p-cluster 'none' --o-visualization silva-heatmap.qzv
qiime feature-table heatmap --i-table gg-table.qza --m-sample-metadata-file metadata.tsv --m-sample-metadata-column diagnosis --p-cluster 'none' --o-visualization gg-heatmap.qzv
qiime feature-table heatmap --i-table gtdb-table.qza --m-sample-metadata-file metadata.tsv --m-sample-metadata-column diagnosis --p-cluster 'none' --o-visualization gtdb-heatmap.qzv
```

## Humann2 Data analysis
First we need to deactivate the previous qiime2 environment, and create a new one for humann2. Change $DIR to your current working directory.
```
conda activate base
conda create -n humann2
conda activate humann2
conda install -c bioconda humann2

mkdir humann2_databases
humann2_databases --download chocophlan full $DIR/humann2_databases
humann2 databases --download uniref uniref50_diamond /home/cpecka/16s_comparison/humann2_databases

cd data

humann2 –input SRR5883625.2-fastq/SRR5883625.2.fastq –output SRR5883625.2-fastq/
humann2 –input SRR5883626.2-fastq/SRR5883626.2.fastq –output SRR5883626.2-fastq/
humann2 –input SRR5883627.2-fastq/SRR5883627.2.fastq –output SRR5883627.2-fastq/
humann2 –input SRR5883628.2-fastq/SRR5883628.2.fastq –output SRR5883628.2-fastq/
humann2 –input SRR5883629.2-fastq/SRR5883629.2.fastq –output SRR5883629.2-fastq/
humann2 –input SRR5883630.2-fastq/SRR5883630.2.fastq –output SRR5883630.2-fastq/
```

Data analysis was carried out using publicly available code on GitHub: https://gitlab.com/JoanML/colonbiome-pilot/-/tree/master/. This step requires cloning the publicly available repository using Git. For Git installation instructions, see the following link: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git 

```
git clone https://gitlab.com/JoanML/colonbiome-pilot.git
```
