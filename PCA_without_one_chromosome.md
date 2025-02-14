# PCA without one chromosome 

### For chromosome NW_024712526.1
#### Set file prefix (without extension)
````
PREFIX="Lobster1MB"
````
#### Extraire les SNPs qui ne sont PAS sur NW_024712526.1
````
awk '$1 != "NW_024712526.1" {print $2}' ${PREFIX}.bim > keep_snps.txt
````
#### Creating a filtered file with PLINK
````
plink --bfile $PREFIX --extract keep_snps.txt --make-bed --out ${PREFIX}_filtered --allow-extra-chr
````
#### Performing a PCR with PLINK
````
plink --bfile ${PREFIX}_filtered --pca --out pca_results --allow-extra-chr
````
#### Load the necessary libraries
````
library(ggplot2)
````
#### Load PCA results from PLINK
````
pca_data <- read.table("pca_results.eigenvec", header = FALSE)
````
#### Rename columns for greater clarity
````
colnames(pca_data) <- c("FID", "IID", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")
````
#### Drawing the first factorial plane
````
ggplot(pca_data, aes(x = PC1, y = PC2)) +
  geom_point() +
  theme_minimal() +
  labs(title = "American Lobster PCA on chromosome NW_024712526.1", x = "PC1", y = "PC2")
````



  
