# PC1 loading per position

# Variables d'entrée
plink_prefix="Lobster1MB_chrNW_024712526.1"  # Préfixe des fichiers PLINK (.bed, .bim, .fam)
output_prefix="ACP_chr_NW_024712526.1"       # Préfixe des fichiers de sortie

# Exécution de la PCA avec PLINK 2
./plink2 --bfile ${plink_prefix} \
       --pca biallelic-var-wts \
       --out ${output_prefix}

# Vérification de la sortie
if [ -f "${output_prefix}.eigenvec.var" ]; then
    echo "Fichier ${output_prefix}.eigenvec.var généré avec succès."
else
    echo "Erreur : le fichier .eigenvec.var n'a pas été généré."
fi

# Chargement des bibliothèques ggplot2 et dplyr pour le traitement des données
library(ggplot2)
library(dplyr)

# Chargement des coefficients de chargement PCA à partir du fichier de sortie
pca_loadings <- read.table("ACP_chr_NW_024712526.1.eigenvec.var", header=FALSE)

# Renommage des colonnes pour une meilleure lisibilité
colnames(pca_loadings) <- c("Chromosome", "SNP", "A1", "A2", "PC1_Loading", "PC2_Loading", "PC3_Loading", 
                            "PC4_Loading", "PC5_Loading", "PC6_Loading", "PC7_Loading", 
                            "PC8_Loading", "PC9_Loading", "PC10_Loading")

# Chargement du fichier BIM contenant les positions des SNPs
bim_data <- read.table("Lobster1MB.bim", header=FALSE)

# Renommage des colonnes du fichier BIM pour une meilleure compréhension
colnames(bim_data) <- c("CHR", "SNP", "Genetic_Dist", "Position", "Allele1", "Allele2")

# Filtrage pour ne conserver que le chromosome d'intérêt
bim_chr <- bim_data %>% filter(CHR == "NW_024712526.1")

# Fusion des données PCA et BIM pour récupérer les positions des SNPs
pca_pos <- merge(pca_loadings, bim_chr, by="SNP")

# Visualisation de PC1_Loading en fonction de la position sur le chromosome
ggplot(pca_pos, aes(x = Position, y = PC1_Loading)) +
  geom_point(alpha = 0.5, color = "blue") +
  theme_minimal() +
  labs(title = "Distribution des SNPs sur le chromosome NW_024712526.1",
       x = "Position des SNPs sur le chromosome",
       y = "PC1 Loading")
