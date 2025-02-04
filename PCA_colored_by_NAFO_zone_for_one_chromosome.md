# Remplacer la colonne des identifiants SNPs par le numero de leur ligne pour qu'ils aient un identifiant unique
awk '{ $2 = NR; print }' Lobster1MB.bim > Lobster1MB2.bim

# Pour le chromosome NW_024712526.1
# Filtrer le fichier .bim en Bash
awk '$1 == "NW_024712526.1"' Lobster1MB2.bim > Lobster1MB_NW_024712526.1.bim

./plink --bfile Lobster1MB --extract Lobster1MB_NW_024712526.1 --make-bed --out Lobster1MB_chrNW_024712526.1 --allow-extra-chr 

# Faire l'ACP pour le chromosome voulu
./plink --bfile Lobster1MB_chrNW_024712526.1 --pca 10 --out ACP_chr_NW_024712526.1 --allow-extra-chr 

# Charger les librairies
library(ggplot2)

# Charger les résultats PCA
pca_data <- read.table("ACP_chr_NW_024712526.1.eigenvec", header=FALSE)
colnames(pca_data) <- c("ID", "Sample", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")

# Charger les métadonnées depuis le fichier .fam
fam_data <- read.table("Lobster1MB.fam", header=FALSE)
colnames(fam_data) <- c("FamilyID", "Sample", "PaternalID", "MaternalID", "Sex", "Phenotype", "Lat", "Lon", "NAFO")

# Ajouter la zone NAFO aux résultats PCA
pca_merged <- merge(pca_data, fam_data[, c("Sample", "NAFO")], by="Sample")

# Tracer PC1 vs PC2 coloré par la zone NAFO
ggplot(pca_merged, aes(x=PC1, y=PC2, color=NAFO)) +
  geom_point(size=3, alpha=0.8) +
  theme_minimal() +
  xlab("PC1") + 
  ylab("PC2") +
  ggtitle("Lobster PCA (PC1 vs PC2, colored by NAFO zone)") +
  scale_color_brewer(palette="Set1")

# Pour le chromosome NW_024713129.1
# Filtrer le fichier .bim en Bash
awk '$1 == "NW_024713129.1"' Lobster1MB.bim > Lobster1MB_NW_024713129.1.bim

./plink --bfile Lobster1MB --extract Lobster1MB_NW_024713129.1.bim --make-bed --out Lobster1MB_chrNW_024713129.1 --allow-extra-chr 

# Faire l'ACP pour le chromosome voulu
./plink --bfile Lobster1MB_chrNW_024713129.1 --pca 10 --out ACP_chr_NW_024713129.1 --allow-extra-chr 

# Charger les librairies
library(ggplot2)

# Charger les résultats PCA
pca_data <- read.table("ACP_chr_NW_024713129.1.eigenvec", header=FALSE)
colnames(pca_data) <- c("ID", "Sample", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")

# Charger les métadonnées depuis le fichier .fam
fam_data <- read.table("Lobster1MB.fam", header=FALSE)
colnames(fam_data) <- c("FamilyID", "Sample", "PaternalID", "MaternalID", "Sex", "Phenotype", "Lat", "Lon", "NAFO")

# Ajouter la zone NAFO aux résultats PCA
pca_merged <- merge(pca_data, fam_data[, c("Sample", "NAFO")], by="Sample")

# Tracer PC1 vs PC2 coloré par la zone NAFO
ggplot(pca_merged, aes(x=PC1, y=PC2, color=NAFO)) +
  geom_point(size=3, alpha=0.8) +
  theme_minimal() +
  xlab("PC1") + 
  ylab("PC2") +
  ggtitle("Lobster PCA (Chromosome NW_024713129.1)") +
  scale_color_brewer(palette="Set1")

# Pour le chromosome NW_024713650.1
# Filtrer le fichier .bim en Bash
awk '$1 == "NW_024713650.1"' Lobster1MB.bim > Lobster1MB_NW_024713650.1.bim

./plink --bfile Lobster1MB --extract Lobster1MB_NW_024713650.1.bim --make-bed --out Lobster1MB_chrNW_024713650.1 --allow-extra-chr 

# Faire l'ACP pour le chromosome voulu
./plink --bfile Lobster1MB_chrNW_024713650.1 --pca 10 --out ACP_chr_NW_024713650.1 --allow-extra-chr 

# Charger les librairies
library(ggplot2)

# Charger les résultats PCA
pca_data <- read.table("ACP_chr_NW_024713650.1.eigenvec", header=FALSE)
colnames(pca_data) <- c("ID", "Sample", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")

# Charger les métadonnées depuis le fichier .fam
fam_data <- read.table("Lobster1MB.fam", header=FALSE)
colnames(fam_data) <- c("FamilyID", "Sample", "PaternalID", "MaternalID", "Sex", "Phenotype", "Lat", "Lon", "NAFO")

# Ajouter la zone NAFO aux résultats PCA
pca_merged <- merge(pca_data, fam_data[, c("Sample", "NAFO")], by="Sample")

# Tracer PC1 vs PC2 coloré par la zone NAFO
ggplot(pca_merged, aes(x=PC1, y=PC2, color=NAFO)) +
  geom_point(size=3, alpha=0.8) +
  theme_minimal() +
  xlab("PC1") + 
  ylab("PC2") +
  ggtitle("Lobster PCA (Chromosome NW_024713650.1)") +
  scale_color_brewer(palette="Set1")

# Pour le chromosome NW_024719665.1
# Filtrer le fichier .bim en Bash
awk '$1 == "NW_024719665.1"' Lobster1MB.bim > Lobster1MB_NW_024719665.1.bim

./plink --bfile Lobster1MB --extract Lobster1MB_NW_024719665.1.bim --make-bed --out Lobster1MB_chrNW_024719665.1 --allow-extra-chr 

# Faire l'ACP pour le chromosome voulu
./plink --bfile Lobster1MB_chrNW_024719665.1 --pca 10 --out ACP_chr_NW_024719665.1 --allow-extra-chr 

# Charger les librairies
library(ggplot2)

# Charger les résultats PCA
pca_data <- read.table("ACP_chr_NW_024719665.1.eigenvec", header=FALSE)
colnames(pca_data) <- c("ID", "Sample", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")

# Charger les métadonnées depuis le fichier .fam
fam_data <- read.table("Lobster1MB.fam", header=FALSE)
colnames(fam_data) <- c("FamilyID", "Sample", "PaternalID", "MaternalID", "Sex", "Phenotype", "Lat", "Lon", "NAFO")

# Ajouter la zone NAFO aux résultats PCA
pca_merged <- merge(pca_data, fam_data[, c("Sample", "NAFO")], by="Sample")

# Tracer PC1 vs PC2 coloré par la zone NAFO
ggplot(pca_merged, aes(x=PC1, y=PC2, color=NAFO)) +
  geom_point(size=3, alpha=0.8) +
  theme_minimal() +
  xlab("PC1") + 
  ylab("PC2") +
  ggtitle("Lobster PCA (Chromosome NW_024719665.1)") +
  scale_color_brewer(palette="Set1")

# Pour le chromosome NW_024737097.1
# Filtrer le fichier .bim en Bash
awk '$1 == "NW_024737097.1"' Lobster1MB.bim > Lobster1MB_NW_024737097.1.bim

./plink --bfile Lobster1MB --extract Lobster1MB_NW_024737097.1.bim --make-bed --out Lobster1MB_chrNW_024737097.1 --allow-extra-chr 

# Faire l'ACP pour le chromosome voulu
./plink --bfile Lobster1MB_chrNW_024737097.1 --pca 10 --out ACP_chr_NW_024737097.1 --allow-extra-chr 

# Charger les librairies
library(ggplot2)

# Charger les résultats PCA
pca_data <- read.table("ACP_chr_NW_024737097.1.eigenvec", header=FALSE)
colnames(pca_data) <- c("ID", "Sample", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")

# Charger les métadonnées depuis le fichier .fam
fam_data <- read.table("Lobster1MB.fam", header=FALSE)
colnames(fam_data) <- c("FamilyID", "Sample", "PaternalID", "MaternalID", "Sex", "Phenotype", "Lat", "Lon", "NAFO")

# Ajouter la zone NAFO aux résultats PCA
pca_merged <- merge(pca_data, fam_data[, c("Sample", "NAFO")], by="Sample")

# Tracer PC1 vs PC2 coloré par la zone NAFO
ggplot(pca_merged, aes(x=PC1, y=PC2, color=NAFO)) +
  geom_point(size=3, alpha=0.8) +
  theme_minimal() +
  xlab("PC1") + 
  ylab("PC2") +
  ggtitle("Lobster PCA (Chromosome NW_024737097.1)") +
  scale_color_brewer(palette="Set1")

# Pour le chromosome NW_024737932.1
# Filtrer le fichier .bim en Bash
awk '$1 == "NW_024737932.1"' Lobster1MB.bim > Lobster1MB_NW_024737932.1.bim

./plink --bfile Lobster1MB --extract Lobster1MB_NW_024737932.1.bim --make-bed --out Lobster1MB_chrNW_024737932.1 --allow-extra-chr 

# Faire l'ACP pour le chromosome voulu
./plink --bfile Lobster1MB_chrNW_024737932.1 --pca 10 --out ACP_chr_NW_024737932.1 --allow-extra-chr 

# Charger les librairies
library(ggplot2)

# Charger les résultats PCA
pca_data <- read.table("ACP_chr_NW_024737932.1.eigenvec", header=FALSE)
colnames(pca_data) <- c("ID", "Sample", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")

# Charger les métadonnées depuis le fichier .fam
fam_data <- read.table("Lobster1MB.fam", header=FALSE)
colnames(fam_data) <- c("FamilyID", "Sample", "PaternalID", "MaternalID", "Sex", "Phenotype", "Lat", "Lon", "NAFO")

# Ajouter la zone NAFO aux résultats PCA
pca_merged <- merge(pca_data, fam_data[, c("Sample", "NAFO")], by="Sample")

# Tracer PC1 vs PC2 coloré par la zone NAFO
ggplot(pca_merged, aes(x=PC1, y=PC2, color=NAFO)) +
  geom_point(size=3, alpha=0.8) +
  theme_minimal() +
  xlab("PC1") + 
  ylab("PC2") +
  ggtitle("Lobster PCA (Chromosome NW_024737932.1)") +
  scale_color_brewer(palette="Set1")

# Pour le chromosome NW_024743709.1
# Filtrer le fichier .bim en Bash
awk '$1 == "NW_024743709.1"' Lobster1MB.bim > Lobster1MB_NW_024743709.1.bim

./plink --bfile Lobster1MB --extract Lobster1MB_NW_024743709.1.bim --make-bed --out Lobster1MB_chrNW_024743709.1 --allow-extra-chr 

# Faire l'ACP pour le chromosome voulu
./plink --bfile Lobster1MB_chrNW_024743709.1 --pca 10 --out ACP_chr_NW_024743709.1 --allow-extra-chr 

# Charger les librairies
library(ggplot2)

# Charger les résultats PCA
pca_data <- read.table("ACP_chr_NW_024743709.1.eigenvec", header=FALSE)
colnames(pca_data) <- c("ID", "Sample", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")

# Charger les métadonnées depuis le fichier .fam
fam_data <- read.table("Lobster1MB.fam", header=FALSE)
colnames(fam_data) <- c("FamilyID", "Sample", "PaternalID", "MaternalID", "Sex", "Phenotype", "Lat", "Lon", "NAFO")

# Ajouter la zone NAFO aux résultats PCA
pca_merged <- merge(pca_data, fam_data[, c("Sample", "NAFO")], by="Sample")

# Tracer PC1 vs PC2 coloré par la zone NAFO
ggplot(pca_merged, aes(x=PC1, y=PC2, color=NAFO)) +
  geom_point(size=3, alpha=0.8) +
  theme_minimal() +
  xlab("PC1") + 
  ylab("PC2") +
  ggtitle("Lobster PCA (Chromosome NW_024743709.1)") +
  scale_color_brewer(palette="Set1")

# Pour le chromosome NW_024744839.1
# Filtrer le fichier .bim en Bash
awk '$1 == "NW_024744839.1"' Lobster1MB.bim > Lobster1MB_NW_024744839.1.bim

./plink --bfile Lobster1MB --extract Lobster1MB_NW_024744839.1.bim --make-bed --out Lobster1MB_chrNW_024744839.1 --allow-extra-chr 

# Faire l'ACP pour le chromosome voulu
./plink --bfile Lobster1MB_chrNW_024744839.1 --pca 10 --out ACP_chr_NW_024744839.1 --allow-extra-chr 

# Charger les librairies
library(ggplot2)

# Charger les résultats PCA
pca_data <- read.table("ACP_chr_NW_024744839.1.eigenvec", header=FALSE)
colnames(pca_data) <- c("ID", "Sample", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")

# Charger les métadonnées depuis le fichier .fam
fam_data <- read.table("Lobster1MB.fam", header=FALSE)
colnames(fam_data) <- c("FamilyID", "Sample", "PaternalID", "MaternalID", "Sex", "Phenotype", "Lat", "Lon", "NAFO")

# Ajouter la zone NAFO aux résultats PCA
pca_merged <- merge(pca_data, fam_data[, c("Sample", "NAFO")], by="Sample")

# Tracer PC1 vs PC2 coloré par la zone NAFO
ggplot(pca_merged, aes(x=PC1, y=PC2, color=NAFO)) +
  geom_point(size=3, alpha=0.8) +
  theme_minimal() +
  xlab("PC1") + 
  ylab("PC2") +
  ggtitle("Lobster PCA (Chromosome NW_024744839.1)") +
  scale_color_brewer(palette="Set1")


