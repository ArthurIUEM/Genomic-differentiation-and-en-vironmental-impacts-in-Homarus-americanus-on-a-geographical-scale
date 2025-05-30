# PCA colored by NAFO zone

# Remplacement de la colonne des identifiants SNP par leur numéro de ligne pour garantir des identifiants uniques
awk '{ $2 = NR; print }' Lobster1MB.bim > Lobster1MB2.bim

# Filtrage du fichier .bim pour conserver uniquement les SNPs du chromosome NW_024712526.1
awk '$1 == "024712526"' Lobster1MB.bim | cut -f2 > snps_chrNW_024712526.1.txt

# Extraction et création d'un nouveau fichier .bed pour le chromosome ciblé
./plink --bfile Lobster1MB \
  --extract snps_chrNW_024712526.1.txt \
  --make-bed \
  --out Lobster1MB_chrNW_024712526.1 \
  --allow-extra-chr

# Exécution de l'ACP (PCA) pour le chromosome ciblé
./plink --bfile Lobster1MB_chrNW_024712526.1 --pca 10 --out ACP_chr_NW_024712526.1 --allow-extra-chr 

# Chargement des bibliothèques nécessaires
library(ggplot2)

# Chargement des résultats de l'ACP
pca_data <- read.table("ACP_chr_NW_024712526.1.eigenvec", header=FALSE)
colnames(pca_data) <- c("ID", "Sample", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")

# Chargement des métadonnées à partir du fichier .fam
fam_data <- read.table("Lobster1MB.fam", header=FALSE)
colnames(fam_data) <- c("FamilyID", "Sample", "PaternalID", "MaternalID", "Sex", "Phenotype", "Lat", "Lon", "NAFO")

# Fusion des résultats PCA avec les métadonnées pour inclure les zones NAFO
pca_merged <- merge(pca_data, fam_data[, c("Sample", "NAFO")], by="Sample")

# Graphique de PC1 vs PC2 coloré par zone NAFO
ggplot(pca_merged, aes(x=PC1, y=PC2, color=NAFO)) +
  geom_point(size=3, alpha=0.8) +
  theme_minimal() +
  xlab("PC1") + 
  ylab("PC2") +
  ggtitle("Lobster PCA coloré par zone NAFO (chromosome NW_024712526.1)") +
  scale_color_brewer(palette="Set1")
