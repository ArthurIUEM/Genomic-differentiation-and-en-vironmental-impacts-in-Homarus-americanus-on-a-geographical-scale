# PC1 loading per SNPs poisition on chromosome

### Input variables
````
plink_prefix="Lobster1MB_chrNW_024712526.1"  # Prefix for PLINK files (.bed, .bim, .fam)
output_prefix="ACP_chr_NW_024712526.1"       # Prefix for output files
````
### PCA execution with PLINK 2 
````
./plink2 --bfile ${plink_prefix} \
       --pca biallelic-var-wts \
       --out ${output_prefix}
````
### Output check
````
if [ -f "${output_prefix}.eigenvec.var" ]; then
    echo "Fichier ${output_prefix}.eigenvec.var généré avec succès."
else
    echo "Erreur : le fichier .eigenvec.var n'a pas été généré."
fi
````
### Load ggplot2 and dplyr for data processing
````
library(ggplot2)
library(dplyr)
````
### Load SNP loadings from PCA
````
pca_loadings <- read.table("ACP_chr_NW_024712526.1.eigenvec.var", header=FALSE)
````
### Rename columns for clarity
````
colnames(pca_loadings) <- c("Chromosome", "SNP", "A1", "A2", "PC1_Loading", "PC2_Loading", "PC3_Loading", 
                            "PC4_Loading", "PC5_Loading", "PC6_Loading", "PC7_Loading", 
                            "PC8_Loading", "PC9_Loading", "PC10_Loading")
````
### Upload BIM file
````
bim_data <- read.table("Lobster1MB.bim", header=FALSE)
````
### Rename BIM file columns
````
colnames(bim_data) <- c("CHR", "SNP", "Genetic_Dist", "Position", "Allele1", "Allele2")
````
### Filter only the chromosome of interest
````
bim_chr <- bim_data %>% filter(CHR == "NW_024712526.1")
````
### Merge PCA and BIM data to retrieve SNP positions
````
pca_pos <- merge(pca_loadings, bim_chr, by="SNP")
````
### Plot PC1_Loading (X) vs. Position on chromosome (Y)
````
ggplot(pca_pos, aes(x = Position, y = PC1_Loading)) +
  geom_point(alpha = 0.5, color = "blue") +
  theme_minimal() +
  labs(title = "Distribution of SNPs on the chromosome NW_024712526.1",
       x = "Position of SNPs on the chromosome",
       y = "PC1 Loading")
````
