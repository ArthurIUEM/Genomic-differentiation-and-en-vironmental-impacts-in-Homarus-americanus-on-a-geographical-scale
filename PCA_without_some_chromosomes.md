# PCA without some chromosomes

# Définir le préfixe du fichier
PREFIX="Lobster1MB"

# Exclure plusieurs chromosomes et extraire les SNPs restants
awk '$1 != "NW_024712526.1" && $1 != "NW_024713650.1" && $1 != "NW_024737932.1" {print $2}' ${PREFIX}.bim > keep_snps.txt

# Créer un fichier filtré avec PLINK
./plink --bfile $PREFIX --extract keep_snps.txt --make-bed --out ${PREFIX}_filtered --allow-extra-chr

# Effectuer une ACP avec PLINK
./plink --bfile ${PREFIX}_filtered --pca --out pca_results --allow-extra-chr 

# Charger les librairies nécessaires
library(ggplot2)

# Charger les résultats de l'ACP de PLINK
pca_data <- read.table("pca_results.eigenvec", header = FALSE)

# Renommer les colonnes pour plus de clarté
colnames(pca_data) <- c("FID", "IID", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")

# Tracer le premier plan factoriel
ggplot(pca_data, aes(x = PC1, y = PC2)) +
  geom_point() +
  theme_minimal() +
  labs(title = "ACP des Homards américains", x = "PC1", y = "PC2")

# Exclure plusieurs chromosomes et extraire les SNPs restants
awk '$1 != "NW_024713129.1" && $1 != "NW_024719665.1" && $1 != "NW_024737097.1" && $1 != "NW_024743709.1" && $1 != "NW_024744839.1" {print $2}' ${PREFIX}.bim > keep_snps.txt

# Créer un fichier filtré avec PLINK
./plink --bfile $PREFIX --extract keep_snps.txt --make-bed --out ${PREFIX}_filtered --allow-extra-chr

# Effectuer une ACP avec PLINK
./plink --bfile ${PREFIX}_filtered --pca --out pca_results --allow-extra-chr 

# Charger les librairies nécessaires
library(ggplot2)

# Charger les résultats de l'ACP de PLINK
pca_data <- read.table("pca_results.eigenvec", header = FALSE)

# Renommer les colonnes pour plus de clarté
colnames(pca_data) <- c("FID", "IID", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")

# Tracer le premier plan factoriel
ggplot(pca_data, aes(x = PC1, y = PC2)) +
  geom_point() +
  theme_minimal() +
  labs(title = "ACP des Homards américains", x = "PC1", y = "PC2")

# Exclure les chromosomes spécifiés en utilisant awk
awk '!($1 == "NW_024712526.1" || $1 == "NW_024713650.1" || $1 == "NW_024737932.1" || $1 == "MW_024713129.1" || $1 == "NW_024719665.1" || $1 == "NW_024737097.1" || $1 == "NW_024743709.1" || $1 == "NW_024744839.1") {print $2}' ${PREFIX}.bim > keep_snps.txt

  # Créer un fichier filtré avec PLINK
./plink --bfile $PREFIX --extract keep_snps.txt --make-bed --out ${PREFIX}_filtered --allow-extra-chr

# Effectuer une ACP avec PLINK
./plink --bfile ${PREFIX}_filtered --pca --out pca_results --allow-extra-chr 

# Charger les librairies nécessaires
library(ggplot2)

# Charger les résultats de l'ACP de PLINK
pca_data <- read.table("pca_results.eigenvec", header = FALSE)

# Renommer les colonnes pour plus de clarté
colnames(pca_data) <- c("FID", "IID", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")

# Tracer le premier plan factoriel
ggplot(pca_data, aes(x = PC1, y = PC2)) +
  geom_point() +
  theme_minimal() +
  labs(title = "ACP des Homards américains", x = "PC1", y = "PC2")
