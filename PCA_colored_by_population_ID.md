# PCA colored by population_ID

Keep ACP realise for NAFO zones 

### Load the necessary libraries
````
library(readxl)
library(dplyr)
````
### Load files
````
fam_data <- read.table("Lobster1MB.fam", header = FALSE, stringsAsFactors = FALSE)
excel_data <- read_excel("Merged_Lobster_data.xlsx")
````
### Check that the column “Sample ID 2” exists in the Excel file
````
colnames(excel_data)
````
### Merge the two files on the Sample ID 2 column (Excel) and the first column of the .fam file.
````
merged_data <- fam_data %>%
  left_join(excel_data, by = c("V1" = "Sample ID 2")) %>%
  select(V1, V2, V3, V4, V5, V6, PopulationID = `Population ID`)  # Utiliser 'Population ID' exactement
````
### Save modified file
````
write.table(merged_data, "Lobster1MB_with_population.fam", row.names = FALSE, col.names = FALSE, quote = FALSE, sep = "\t")
````
### Load libraries
````
library(ggplot2)
````
### Manually define 21 distinct colors
````
colors_21 <- c(
  "#E41A1C", "#377EB8", "#4DAF4A", "#FF7F00", "#FFFF33",
  "#A65628", "#FF1493", "#999999", "#66C2A5", "#FC8D62",
  "#8DA0CB", "#E78AC3", "#A6D854", "black", "#FFD92F",
  "#E5C494", "#B3B3B3", "#1B9E77", "#D95F02", "#7570B3",
  "#4B0082"
)
````
### For the chromosome NW_024712526.1
#### Load PCA results
````
pca_data1 <- read.table("ACP_chr_NW_024712526.1.eigenvec", header=FALSE)
colnames(pca_data1) <- c("ID", "Sample", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")
````
#### Load metadata from .fam file
````
fam_data <- read.table("Lobster1MB_with_population.fam", header=FALSE)
colnames(fam_data) <- c("FamilyID", "Sample", "PaternalID", "MaternalID", "Sex", "Phenotype", "Pop_ID")
````
#### Add Pop_ID zone to PCA results
````
pca_merged1 <- merge(pca_data1, fam_data[, c("Sample", "Pop_ID")], by="Sample")
````
#### Plot the graph 
````
ggplot(pca_merged, aes(x=PC1, y=PC2, color=Pop_ID)) +
  geom_point(size=3, alpha=0.8) +
  theme_minimal() +
  xlab("PC1") + 
  ylab("PC2") +
  ggtitle("Lobster PCA colored by populations (Chromosome NW_024712526.1)") +
  scale_color_manual(values = colors_21) 
````
