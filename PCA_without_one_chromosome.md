# PCA without one chromosome 

### For chromosome NW_024712526.1
#### Set file prefix (without extension)
PREFIX="Lobster1MB"

#### Extraire les SNPs qui ne sont PAS sur NW_024712526.1
awk '$1 != "NW_024712526.1" {print $2}' ${PREFIX}.bim > keep_snps.txt

#### Creating a filtered file with PLINK
plink --bfile $PREFIX --extract keep_snps.txt --make-bed --out ${PREFIX}_filtered --allow-extra-chr

#### Performing a PCR with PLINK
plink --bfile ${PREFIX}_filtered --pca --out pca_results --allow-extra-chr

#### Load the necessary libraries
library(ggplot2)

#### Load PCA results from PLINK
pca_data <- read.table("pca_results.eigenvec", header = FALSE)

#### Rename columns for greater clarity
colnames(pca_data) <- c("FID", "IID", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")

#### Drawing the first factorial plane
ggplot(pca_data, aes(x = PC1, y = PC2)) +
  geom_point() +
  theme_minimal() +
  labs(title = "American Lobster PCA on chromosome NW_024712526.1", x = "PC1", y = "PC2")


### For chromosome NW_024713129.1
#### Extraire les SNPs qui ne sont PAS sur NW_024713129.1
awk '$1 != "NW_024713129.1" {print $2}' ${PREFIX}.bim > keep_snps2.txt

#### Creating a filtered file with PLINK
./plink --bfile $PREFIX --extract keep_snps2.txt --make-bed --out ${PREFIX}_filtered2 --allow-extra-chr

#### Performing a PCR with PLINK
./plink --bfile ${PREFIX}_filtered2 --pca --out pca_results2 --allow-extra-chr

#### Load PCA results from PLINK
pca_data <- read.table("pca_results2.eigenvec", header = FALSE)

#### Rename columns for greater clarity
colnames(pca_data) <- c("FID", "IID", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")

#### Drawing the first factorial plane
ggplot(pca_data, aes(x = PC1, y = PC2)) +
  geom_point() +
  theme_minimal() +
  labs(title = "American Lobster PCA on chromosome NW_024713129.1", x = "PC1", y = "PC2")


### For chromosome NW_024713650.1
#### Extraire les SNPs qui ne sont PAS sur NW_024713650.1
awk '$1 != "NW_024713650.1" {print $2}' ${PREFIX}.bim > keep_snps3.txt

#### Creating a filtered file with PLINK
./plink --bfile $PREFIX --extract keep_snps3.txt --make-bed --out ${PREFIX}_filtered3 --allow-extra-chr

#### Performing a PCR with PLINK
./plink --bfile ${PREFIX}_filtered3 --pca --out pca_results3 --allow-extra-chr

#### Load PCA results from PLINK
pca_data <- read.table("pca_results3.eigenvec", header = FALSE)

#### Rename columns for greater clarity
colnames(pca_data) <- c("FID", "IID", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")

#### Drawing the first factorial plane
ggplot(pca_data, aes(x = PC1, y = PC2)) +
  geom_point() +
  theme_minimal() +
  labs(title = "American Lobster PCA on chromosome NW_024713650.1", x = "PC1", y = "PC2")


### For chromosome NW_024719665.1
#### Extraire les SNPs qui ne sont PAS sur NW_024719665.1
awk '$1 != "NW_024719665.1" {print $2}' ${PREFIX}.bim > keep_snps4.txt

#### Creating a filtered file with PLINK
./plink --bfile $PREFIX --extract keep_snps4.txt --make-bed --out ${PREFIX}_filtered4 --allow-extra-chr

#### Performing a PCR with PLINK
./plink --bfile ${PREFIX}_filtered4 --pca --out pca_results4 --allow-extra-chr

#### Load PCA results from PLINK
pca_data <- read.table("pca_results4.eigenvec", header = FALSE)

#### Rename columns for greater clarity
colnames(pca_data) <- c("FID", "IID", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")

#### Drawing the first factorial plane
ggplot(pca_data, aes(x = PC1, y = PC2)) +
  geom_point() +
  theme_minimal() +
  labs(title = "American Lobster PCA on chromosome NW_024719665.1", x = "PC1", y = "PC2")

  
### For chromosome NW_024737097.1
#### Extraire les SNPs qui ne sont PAS sur NW_024737097.1
awk '$1 != "NW_024737097.1" {print $2}' ${PREFIX}.bim > keep_snps5.txt

#### Creating a filtered file with PLINK
./plink --bfile $PREFIX --extract keep_snps5.txt --make-bed --out ${PREFIX}_filtered5 --allow-extra-chr

#### Performing a PCR with PLINK
./plink --bfile ${PREFIX}_filtered5 --pca --out pca_results5 --allow-extra-chr

#### Load PCA results from PLINK
pca_data <- read.table("pca_results5.eigenvec", header = FALSE)

#### Rename columns for greater clarity
colnames(pca_data) <- c("FID", "IID", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")

#### Drawing the first factorial plane
ggplot(pca_data, aes(x = PC1, y = PC2)) +
  geom_point() +
  theme_minimal() +
  labs(title = "American Lobster PCA on chromosome NW_024737097.1", x = "PC1", y = "PC2")

  
### For chromosome NW_024737932.1
#### Extraire les SNPs qui ne sont PAS sur NW_024737932.1
awk '$1 != "NW_024737932.1" {print $2}' ${PREFIX}.bim > keep_snps6.txt

#### Creating a filtered file with PLINK
./plink --bfile $PREFIX --extract keep_snps6.txt --make-bed --out ${PREFIX}_filtered6 --allow-extra-chr

#### Performing a PCR with PLINK
./plink --bfile ${PREFIX}_filtered6 --pca --out pca_results6 --allow-extra-chr

#### Load PCA results from PLINK
pca_data <- read.table("pca_results6.eigenvec", header = FALSE)

#### Rename columns for greater clarity
colnames(pca_data) <- c("FID", "IID", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")

#### Drawing the first factorial plane
ggplot(pca_data, aes(x = PC1, y = PC2)) +
  geom_point() +
  theme_minimal() +
  labs(title = "American Lobster PCA on chromosome NW_024737932.1", x = "PC1", y = "PC2")


### For chromosome NW_024743709.1
#### Extraire les SNPs qui ne sont PAS sur NW_024743709.1
awk '$1 != "NW_024743709.1" {print $2}' ${PREFIX}.bim > keep_snps7.txt

#### Creating a filtered file with PLINK
./plink --bfile $PREFIX --extract keep_snps7.txt --make-bed --out ${PREFIX}_filtered7 --allow-extra-chr

#### Performing a PCR with PLINK
./plink --bfile ${PREFIX}_filtered7 --pca --out pca_results7 --allow-extra-chr

#### Load PCA results from PLINK
pca_data <- read.table("pca_results7.eigenvec", header = FALSE)

#### Rename columns for greater clarity
colnames(pca_data) <- c("FID", "IID", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")

#### Drawing the first factorial plane
ggplot(pca_data, aes(x = PC1, y = PC2)) +
  geom_point() +
  theme_minimal() +
  labs(title = "American Lobster PCA on chromosome NW_024743709.1", x = "PC1", y = "PC2")

  
### For chromosome NW_024744839.1
#### Extraire les SNPs qui ne sont PAS sur NW_024744839.1
awk '$1 != "NW_024744839.1" {print $2}' ${PREFIX}.bim > keep_snps8.txt

#### Creating a filtered file with PLINK
./plink --bfile $PREFIX --extract keep_snps8.txt --make-bed --out ${PREFIX}_filtered8 --allow-extra-chr

#### Performing a PCR with PLINK
./plink --bfile ${PREFIX}_filtered8 --pca --out pca_results8 --allow-extra-chr

#### Load PCA results from PLINK
pca_data <- read.table("pca_results8.eigenvec", header = FALSE)

#### Rename columns for greater clarity
colnames(pca_data) <- c("FID", "IID", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")

#### Drawing the first factorial plane
ggplot(pca_data, aes(x = PC1, y = PC2)) +
  geom_point() +
  theme_minimal() +
  labs(title = "American Lobster PCA on chromosome NW_024744839.1", x = "PC1", y = "PC2")

  
