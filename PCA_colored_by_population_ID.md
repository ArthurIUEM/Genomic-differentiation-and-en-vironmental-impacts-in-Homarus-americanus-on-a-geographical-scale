# PCA colored by population_ID

Garder l'ACP realise pour les zones NAFO 

### Charger les bibliothèques nécessaires
library(readxl)
library(dplyr)

### Charger les fichiers
fam_data <- read.table("Lobster1MB.fam", header = FALSE, stringsAsFactors = FALSE)
excel_data <- read_excel("Merged_Lobster_data.xlsx")

### Assurez-vous que la colonne "Sample ID 2" existe dans le fichier Excel
colnames(excel_data)

### Fusionner les deux fichiers sur la colonne Sample ID 2 (Excel) et la première colonne du fichier .fam
merged_data <- fam_data %>%
  left_join(excel_data, by = c("V1" = "Sample ID 2")) %>%
  select(V1, V2, V3, V4, V5, V6, PopulationID = `Population ID`)  # Utiliser 'Population ID' exactement

### Enregistrer le fichier modifié
write.table(merged_data, "Lobster1MB_with_population.fam", row.names = FALSE, col.names = FALSE, quote = FALSE, sep = "\t")


### Load libraries

library(ggplot2)

### Définir manuellement 21 couleurs distinctes
colors_21 <- c(
  "#E41A1C", "#377EB8", "#4DAF4A", "#FF7F00", "#FFFF33",
  "#A65628", "#FF1493", "#999999", "#66C2A5", "#FC8D62",
  "#8DA0CB", "#E78AC3", "#A6D854", "black", "#FFD92F",
  "#E5C494", "#B3B3B3", "#1B9E77", "#D95F02", "#7570B3",
  "#4B0082"
)

### Pour le chromosome
#### Load PCA results

pca_data1 <- read.table("ACP_chr_NW_024712526.1.eigenvec", header=FALSE)
colnames(pca_data1) <- c("ID", "Sample", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")

#### Load metadata from .fam file

fam_data <- read.table("Lobster1MB_with_population.fam", header=FALSE)
colnames(fam_data) <- c("FamilyID", "Sample", "PaternalID", "MaternalID", "Sex", "Phenotype", "Pop_ID")

#### Add Pop_ID zone to PCA results

pca_merged1 <- merge(pca_data1, fam_data[, c("Sample", "Pop_ID")], by="Sample")


#### Tracer le graphique 
ggplot(pca_merged, aes(x=PC1, y=PC2, color=Pop_ID)) +
  geom_point(size=3, alpha=0.8) +
  theme_minimal() +
  xlab("PC1") + 
  ylab("PC2") +
  ggtitle("Lobster PCA colored by populations (Chromosome NW_024712526.1)") +
  scale_color_manual(values = colors_21)  # Utiliser les couleurs définies manuellement

### Pour le chromosome
#### Load PCA results

pca_data2 <- read.table("ACP_chr_NW_024713129.1.eigenvec", header=FALSE)
colnames(pca_data2) <- c("ID", "Sample", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")

#### Add Pop_ID zone to PCA results

pca_merged2 <- merge(pca_data2, fam_data[, c("Sample", "Pop_ID")], by="Sample")

#### Tracer le graphique 
ggplot(pca_merged2, aes(x=PC1, y=PC2, color=Pop_ID)) +
  geom_point(size=3, alpha=0.8) +
  theme_minimal() +
  xlab("PC1") + 
  ylab("PC2") +
  ggtitle("Lobster PCA colored by populations (Chromosome NW_024713129.1)") +
  scale_color_manual(values = colors_21)  # Utiliser les couleurs définies manuellement

### Pour le chromosome
#### Load PCA results

pca_data3 <- read.table("ACP_chr_NW_024713650.1.eigenvec", header=FALSE)
colnames(pca_data3) <- c("ID", "Sample", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")

#### Add Pop_ID zone to PCA results

pca_merged3 <- merge(pca_data3, fam_data[, c("Sample", "Pop_ID")], by="Sample")

#### Tracer le graphique 
ggplot(pca_merged3, aes(x=PC1, y=PC2, color=Pop_ID)) +
  geom_point(size=3, alpha=0.8) +
  theme_minimal() +
  xlab("PC1") + 
  ylab("PC2") +
  ggtitle("Lobster PCA colored by populations (Chromosome NW_024713650.1)") +
  scale_color_manual(values = colors_21)  # Utiliser les couleurs définies manuellement

### Pour le chromosome
#### Load PCA results

pca_data4 <- read.table("ACP_chr_NW_024719665.1.eigenvec", header=FALSE)
colnames(pca_data4) <- c("ID", "Sample", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")

#### Add Pop_ID zone to PCA results

pca_merged4 <- merge(pca_data4, fam_data[, c("Sample", "Pop_ID")], by="Sample")

#### Tracer le graphique 
ggplot(pca_merged4, aes(x=PC1, y=PC2, color=Pop_ID)) +
  geom_point(size=3, alpha=0.8) +
  theme_minimal() +
  xlab("PC1") + 
  ylab("PC2") +
  ggtitle("Lobster PCA colored by populations (Chromosome NW_024719665.1)") +
  scale_color_manual(values = colors_21)  # Utiliser les couleurs définies manuellement

### Pour le chromosome
#### Load PCA results

pca_data5 <- read.table("ACP_chr_NW_024737097.1.eigenvec", header=FALSE)
colnames(pca_data5) <- c("ID", "Sample", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")

#### Add Pop_ID zone to PCA results

pca_merged5 <- merge(pca_data5, fam_data[, c("Sample", "Pop_ID")], by="Sample")

#### Tracer le graphique 
ggplot(pca_merged5, aes(x=PC1, y=PC2, color=Pop_ID)) +
  geom_point(size=3, alpha=0.8) +
  theme_minimal() +
  xlab("PC1") + 
  ylab("PC2") +
  ggtitle("Lobster PCA colored by populations (Chromosome NW_024737097.1)") +
  scale_color_manual(values = colors_21)  # Utiliser les couleurs définies manuellement

### Pour le chromosome
#### Load PCA results

pca_data6 <- read.table("ACP_chr_NW_024737932.1.eigenvec", header=FALSE)
colnames(pca_data6) <- c("ID", "Sample", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")

#### Add Pop_ID zone to PCA results

pca_merged6 <- merge(pca_data6, fam_data[, c("Sample", "Pop_ID")], by="Sample")

#### Tracer le graphique 
ggplot(pca_merged6, aes(x=PC1, y=PC2, color=Pop_ID)) +
  geom_point(size=3, alpha=0.8) +
  theme_minimal() +
  xlab("PC1") + 
  ylab("PC2") +
  ggtitle("Lobster PCA colored by populations (Chromosome NW_024737932.1)") +
  scale_color_manual(values = colors_21)  # Utiliser les couleurs définies manuellement

### Pour le chromosome
#### Load PCA results

pca_data7 <- read.table("ACP_chr_NW_024743709.1.eigenvec", header=FALSE)
colnames(pca_data7) <- c("ID", "Sample", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")

#### Add Pop_ID zone to PCA results

pca_merged7 <- merge(pca_data7, fam_data[, c("Sample", "Pop_ID")], by="Sample")

#### Tracer le graphique 
ggplot(pca_merged7, aes(x=PC1, y=PC2, color=Pop_ID)) +
  geom_point(size=3, alpha=0.8) +
  theme_minimal() +
  xlab("PC1") + 
  ylab("PC2") +
  ggtitle("Lobster PCA colored by populations (Chromosome NW_024743709.1)") +
  scale_color_manual(values = colors_21)  # Utiliser les couleurs définies manuellement

### Pour le chromosome
#### Load PCA results

pca_data8 <- read.table("ACP_chr_NW_024744839.1.eigenvec", header=FALSE)
colnames(pca_data8) <- c("ID", "Sample", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")

#### Add Pop_ID zone to PCA results

pca_merged8 <- merge(pca_data8, fam_data[, c("Sample", "Pop_ID")], by="Sample")

#### Tracer le graphique 
ggplot(pca_merged8, aes(x=PC1, y=PC2, color=Pop_ID)) +
  geom_point(size=3, alpha=0.8) +
  theme_minimal() +
  xlab("PC1") + 
  ylab("PC2") +
  ggtitle("Lobster PCA colored by populations (Chromosome NW_024744839.1)") +
  scale_color_manual(values = colors_21)  # Utiliser les couleurs définies manuellement
