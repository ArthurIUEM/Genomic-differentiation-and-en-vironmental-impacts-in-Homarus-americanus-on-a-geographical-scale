library(dplyr)
library(ggplot2)
# Charger les fichiers
bim <- read.table("Lobster1MB.bim", header=FALSE, stringsAsFactors=FALSE)
pca_var <- read.table("PCA_results.eigenvec.var", header=FALSE, stringsAsFactors=FALSE)

# Renommer les colonnes du fichier bim
colnames(bim) <- c("Chromosome", "SNP", "GeneticDist", "Position", "Allele1", "Allele2")

# Renommer les colonnes du fichier PCA (on suppose que la 1ère colonne contient les SNPs et la 3e colonne contient les loadings de PC2)
colnames(pca_var) <- c("Chromosome", "SNP", "GeneticDist", "Position", "PC1", "PC2", "PC3", "PC4") # Ajuste si nécessaire

# Filtrer les SNPs appartenant à NW_024712526.1
bim_filtered <- bim %>% filter(Chromosome == "NW_024712526.1")

# Vérifier que les formats des SNPs sont identiques
bim_filtered$SNP <- trimws(bim_filtered$SNP)
pca_var$SNP <- trimws(pca_var$SNP)

# Joindre les fichiers
merged_data <- merge(bim_filtered, pca_var, by="SNP")

# Générer le graphique
ggplot(merged_data, aes(x=Position.x, y=PC2)) +
  geom_point(alpha=0.5, color="blue") +
  theme_minimal() +
  labs(title="PC2 Loadings sur NW_024712526.1", x="Position sur le chromosome", y="PC2 Loading")

