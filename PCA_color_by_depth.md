# PCA colored by sequencing depth

### Run PCA with PLINK
Bash
```
./plink --bfile Lobster1MB --pca 10 --out ACP_Lobster --allow-extra-chr
```
### Load librairies
R
```
library(ggplot2)
```
### Load PCA results
```
pca_data <- read.table("ACP_Lobster.eigenvec", header=FALSE)
colnames(pca_data) <- c("FID", "IID", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")
```
### Load depth file
```
depth_data <- read.table("Lobster1MB.idepth", header=TRUE)
```
### Merge ACP and depth data by sample ID
```
merged_data <- merge(pca_data, depth_data, by.x="IID", by.y="INDV")
```
### Plot PC1 vs PC2 with depth staining
```
ggplot(merged_data, aes(x=PC1, y=PC2, color=MEAN_DEPTH)) +
  geom_point(size=3) +
  scale_color_gradientn(colors=c("blue", "cyan", "yellow", "red"), 
                        values=c(0, 0.3, 0.7, 1), 
                        limits=range(merged_data$MEAN_DEPTH)) +
  theme_minimal() +
  xlab("PC1") + 
  ylab("PC2") +
  ggtitle("PCA of Lobster colored by sequencing depth") +
  labs(color="Sequemcing depth")
```
