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

Note that each step is for reference only, please note the configuration of the specific software and environment, and refer to the corresponding wiki if in doubt。

# 1. Filter vcf file for downstream analysis
---
1. Setup and Directory Creation: First, create a directory VCF_filtering and copy the original VCF file into it.
   ```
    mkdir malaria_population
    cd malaria_population
    cp /home/liuji/data/Pf7_practice_chr13.vcf ./

   ```


2. Individual Filtering: Use vcftools to calculate the missing rate per individual (--missing-indv) and filter out individuals with more than 20% missing data.
   ```
   vcftools --vcf Pf7_practice_chr13.vcf --missing-indv
   awk '$5 > 0.2' out.imiss | cut -f1 > lowDP.indv
   ```
3. Site Filtering: Remove low-quality individuals and filter variant sites with vcftools, setting thresholds for missing data, alleles, and quality scores.
   ```
   vcftools --vcf Pf7_practice_chr13.vcf --max-missing 0.8 --max-alleles 2 --minQ 30 --remove-filtered-all --remove lowDP.indv --recode --recode-INFO-all --out Pf7_practice_chr13.filtered
   ```
4. SNP Selection: Retain only SNPs by saving filtered VCF results to a SNP-specific file for downstream analyses.
   ```
   cat Pf7_practice_chr13.filtered.recode.vcf > Pf7_practice_chr13.filtered.snps.vcf
   ```

---

# 2. PCA & Admixture Analysis & Fst
  ## PCA
   1. Prepare Files in PLINK: Convert the VCF file into PLINK format using plink2 to generate .bed, .fam, .ped, and .bim files. These are necessary for performing downstream analyses like admixture analysis.
  ```
  plink2 --vcf Pf7_practice_chr13.filtered.snps.vcf --make-bed --allow-extra-chr --out Pf7
  ```
   2. Generate Eigenvalues: Use PLINK to perform PCA (--pca command) on the prepared files to generate eigenvalues and eigenvectors. These values help explain the genetic variance across samples.
  ```
  plink2 --bfile Pf7 --pca --allow-extra-chr --out Pf7 
  ```
3. Transfer Eigenvector Files: Transfer the A_alb.eigenvec file from the server to a local machine for further analysis.
   
  plotting the PCA output by yourself

  ## Admixture Analysis
  1. Generating Input File: Modify the chromosome names by replacing the first column in the .bim file with 0 to make it compatible with admixture.
  ```
  awk '{$1="0";print $0}' Pf7.bim > Pf7.bim.tmp
  mv Pf7.bim.tmp Pf7.bim
  ```
  The next steps are based on the actual situation
  2. Running Admixture: Use the admixture command with --cv to perform clustering. This will generate files with cluster assignments (.Q) and SNP allele frequencies (.P).
  3. Determining Optimal K: Run admixture with different values of K (e.g., 3 to 5), then check the cross-validation error to identify the best K by analyzing the output .cv.error.


  plotting the Admixture output by yourself

  ## Fst
  1. Install tools: Use conda to install bcftools and related dependencies.
  2. Random splitting: Extract sample list from VCF file, shuffle, and split into two groups.
  3. Fst Calculation: Use vcftools to calculate Fst values between the two groups.
  4. Window-based Fst: Calculate Fst values for specific genomic windows.
  5. Filter differentiated regions: Use awk to filter high Fst values, indicating regions of significant genetic differentiation.

# 3. SNP_Phylo
  1. Prepare Environment: Install required software using conda and activate the environment.
  2. Create Work Directory: Set up a new directory for SNP phylogenetic construction and link the filtered VCF file.
  ```
  mkdir workshop_SNP_PhyloConstruction
  cd workshop_SNP_PhyloConstruction
  ln -s ~/Pf7_practice_chr13.filtered.snps.vcf ./
  ```
  4. Convert VCF to TAB Format: Convert the VCF file into a TAB-delimited format.
  ```
  cat Pf7_practice_chr13.filtered.snps.vcf | vcf-to-tab > Pf7_practice_chr13.filtered.snps.vcf.tab
  ```
  6. Convert TAB to FASTA: Use a custom Perl script to convert the TAB file into a SNP alignment in FASTA format.
  ```
  perl /home/renzirui/workshop_SNP_PhyloConstruction/vcf_tab_to_fasta_alignment.pl -i Pf7_practice_chr13.filtered.snps.vcf.tab > Pf7_practice_chr13.filtered.snps.afa
  ```
  7. Build Phylogenetic Tree: Use IQ-TREE to construct the SNP phylogenetic tree with specified parameters.
  ```
  iqtree -s Pf7_practice_chr13.filtered.snps.afa -m MFP -bb 1000 -nt 4 -pre Pf7_SNP_Phylo/tree
  ```
 # 4. IBD(tentative)
 [refrence](https://www.cog-genomics.org/plink2/ibd)

 # 5. Question after work

How to design and screen target sites for large-scale assessment of population characteristics and resistance information?
