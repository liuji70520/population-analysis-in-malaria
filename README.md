# population-analysis-in-malaria
The data for this task is from PF7.

Pf7 is a global Plasmodium falciparum genomic dataset that primarily supports research on the genetic structure, population dynamics, and drug resistance of P. falciparum (the malaria parasite). This dataset includes extensive single nucleotide polymorphism (SNP) information covering various geographical regions, infection environments, and population characteristics, providing high-resolution data for understanding the genomic diversity and evolutionary processes of P. falciparum.

You can download the data via wget in linux, but note that the data is 34GB in size.

```bash
wget -r ftp://ngs.sanger.ac.uk/production/malaria/Resource/34/Pf7_vcf/
```

---
## Begin with the question, "How can we study the genetic structure characteristics of the target population through whole-genome variation data?" Keep this question in mind throughout the analysis, as it will guide how we interpret population genetic structure.
---

# Data prepare 
```
cp /home/liuji/data/Pf7_practice_chr1.vcf ./
```
# Filter vcf file for downstream analysis
---
1. Setup and Directory Creation: First, create a directory VCF_filtering and copy the original VCF file into it.

2. Individual Filtering: Use vcftools to calculate the missing rate per individual (--missing-indv) and filter out individuals with more than 20% missing data.

3. Site Filtering: Remove low-quality individuals and filter variant sites with vcftools, setting thresholds for missing data, alleles, and quality scores.

4. SNP Selection: Retain only SNPs by saving filtered VCF results to a SNP-specific file for downstream analyses.

---
# PCA & Admixture Analysis & Fst

# SNP_Phylo

# IBD(tentative)

# Question after work
