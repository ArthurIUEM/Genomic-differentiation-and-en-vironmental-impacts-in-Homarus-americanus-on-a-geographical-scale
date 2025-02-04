### Calculation of principal components
```
./plink --bfile Lobster1MB --pca 10 --out ACP_Lobster --allow-extra-chr
```
### Load libraries 
```
library(ggplot2)
```
### Load results
```
pca_data <- read.table("ACP_Lobster.eigenvec", header=FALSE)
colnames(pca_data) <- c("ID", "Sample", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")
```
### Trace PC1 vs PC2
```
ggplot(pca_data, aes(x=PC1, y=PC2)) +
  geom_point() +
  theme_minimal() +
  xlab("PC1") + 
  ylab("PC2") +
  ggtitle("Lobster PCA (PC1/PC2)")
```
### Trace PC3 vs PC4
```
ggplot(pca_data, aes(x=PC3, y=PC4)) +
  geom_point() +
  theme_minimal() +
  xlab("PC3") + 
  ylab("PC4") +
  ggtitle("Lobster PCA (PC3/PC4)")
```
### Trace PC5 vs PC6
```
ggplot(pca_data, aes(x=PC5, y=PC6)) +
  geom_point() +
  theme_minimal() +
  xlab("PC5") + 
  ylab("PC6") +
  ggtitle("Lobster PCA (PC5/PC6)")
```
