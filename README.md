# Translating metabolic pathway functions from dysbiosis data
The gut microbiome affects human health via bacterial production to metabolic pathways. The goal of this project is to understand how multiple members of a bacterial community contribute to metabolic pathways. We illustrate our results by using a computational tool to predict bacterial metabolite contributions in 16S and metagenomic data for Crohn's Disease patients.

## Installation requirements:

### Install qiime2 requirements
For installation instructions, see documentation on qiime's website: https://docs.qiime2.org/2021.11/install/native/#install-qiime-2-within-a-conda-environment

### Data Access
16S and metagenomic data are available on SRA with accession number SRP114403: https://www.ncbi.nlm.nih.gov/sra/?term=SRP114403.

```
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
