# Charger les librairies nécessaires
install.packages("gtools")  # Installe le package si ce n'est pas encore fait
library(gtools)  # Charge le package
library(ggplot2)
library(dplyr)

# Charger les fichiers
bim <- read.table("Lobster1MB.bim", header=FALSE, stringsAsFactors=FALSE)
pca_var <- read.table("PCA_results.eigenvec.var", header=FALSE, stringsAsFactors=FALSE)

# Renommer les colonnes
colnames(bim) <- c("Chromosome", "SNP", "GeneticDist", "Position", "Allele1", "Allele2")
colnames(pca_var) <- c("Chromosome", "SNP", "Allele1", "Allele2", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")  # Ajuste si nécessaire

# Vérifier et convertir les formats
bim$SNP <- trimws(bim$SNP)
pca_var$SNP <- trimws(pca_var$SNP)

# Fusionner les fichiers
merged_data <- merge(bim, pca_var, by="SNP")

# Vérifier et renommer les colonnes de Position s'il y a des doublons
if ("Position.x" %in% colnames(merged_data)) {
  merged_data <- merged_data %>%
    rename(Position = Position.x) %>%
    select(-Position.y)  # Supprime la colonne dupliquée si nécessaire
}

# Convertir les colonnes en numériques
merged_data$Position <- as.numeric(merged_data$Position)
merged_data$PC2 <- as.numeric(merged_data$PC2)

# Trier les chromosomes de façon cohérente
merged_data$Chromosome.x <- factor(merged_data$Chromosome.x, levels = mixedsort(unique(merged_data$Chromosome.x)))

# Créer un index cumulatif pour la position des SNPs
merged_data <- merged_data %>%
  arrange(Chromosome.x, Position) %>%
  group_by(Chromosome.x) %>%
  mutate(cum_position = Position + cumsum(lag(max(Position, na.rm=TRUE), default=0))) %>%
  ungroup()

# Générer une palette alternant les couleurs par chromosome
chromosome_list <- unique(merged_data$Chromosome.x)
color_palette <- rep(c("blue", "red"), length.out = length(chromosome_list))
names(color_palette) <- chromosome_list

# Tracer le Manhattan plot des PC2 loadings
ggplot(merged_data, aes(x=cum_position, y=PC2, color=Chromosome.x)) +
  geom_point(alpha=0.5, size=0.7) +
  scale_x_continuous(label=NULL, breaks=NULL) +  # Pas d'étiquettes sur l'axe X
  scale_color_manual(values=color_palette) +  # Appliquer la palette corrigée
  theme_minimal() +
  labs(title="Genome-Wide PC2 Loadings", x="Position génomique", y="PC2 Loading")
