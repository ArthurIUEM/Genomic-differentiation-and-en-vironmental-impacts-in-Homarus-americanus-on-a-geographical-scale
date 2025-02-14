# PCA color by NAFO zone

### Replace the column of SNP identifiers with their line number so that they have a unique identifier.
````
awk '{ $2 = NR; print }' Lobster1MB.bim > Lobster1MB2.bim
````
### For the chromosome NW_024712526.1
#### Filtering the .bim file in Bash
````
awk '$1 == "NW_024712526.1"' Lobster1MB2.bim > Lobster1MB_NW_024712526.1.bim
````
````
./plink --bfile Lobster1MB --extract Lobster1MB_NW_024712526.1 --make-bed --out Lobster1MB_chrNW_024712526.1 --allow-extra-chr 
````
#### Perform PCA for the desired chromosome
````
./plink --bfile Lobster1MB_chrNW_024712526.1 --pca 10 --out ACP_chr_NW_024712526.1 --allow-extra-chr 
````
#### Load libraries
````
library(ggplot2)
````
#### Load PCA results
````
pca_data <- read.table("ACP_chr_NW_024712526.1.eigenvec", header=FALSE)
colnames(pca_data) <- c("ID", "Sample", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")
````
#### Load metadata from .fam file
````
fam_data <- read.table("Lobster1MB.fam", header=FALSE)
colnames(fam_data) <- c("FamilyID", "Sample", "PaternalID", "MaternalID", "Sex", "Phenotype", "Lat", "Lon", "NAFO")
````
#### Add NAFO zone to PCA results
````
pca_merged <- merge(pca_data, fam_data[, c("Sample", "NAFO")], by="Sample")
````
#### Plot PC1 vs PC2 colored by NAFO zone
````
ggplot(pca_merged, aes(x=PC1, y=PC2, color=NAFO)) +
  geom_point(size=3, alpha=0.8) +
  theme_minimal() +
  xlab("PC1") + 
  ylab("PC2") +
  ggtitle("Lobster PCA colored by NAFO zone (chromosome NW_024712526.1)") +
  scale_color_brewer(palette="Set1")
````
