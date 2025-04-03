# Charger les librairies nécessaires
library(qqman)
library(dplyr)

# Charger les fichiers
bim <- read.table("Lobster1MB.bim", header=FALSE, stringsAsFactors=FALSE)
pca_var <- read.table("PCA_results.eigenvec.var", header=FALSE, stringsAsFactors=FALSE)

# Renommer les colonnes
colnames(bim) <- c("Chromosome", "SNP", "GeneticDist", "Position", "Allele1", "Allele2")
colnames(pca_var) <- c("Chromosome", "SNP", "Allele1", "Allele2", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")

# Vérifier et convertir les formats
bim$SNP <- trimws(bim$SNP)
pca_var$SNP <- trimws(pca_var$SNP)

# Nettoyer les identifiants des chromosomes
bim$Chromosome <- gsub("NW_", "", bim$Chromosome)
bim$Chromosome <- gsub("\\.1", "", bim$Chromosome)
bim$Chromosome <- as.numeric(bim$Chromosome)  # Convertir en numérique
pca_var$Chromosome <- gsub("NW_", "", pca_var$Chromosome)
pca_var$Chromosome <- gsub("\\.1", "", pca_var$Chromosome)
pca_var$Chromosome <- as.numeric(pca_var$Chromosome)  # Convertir en numérique

# Fusionner les fichiers
merged_data <- merge(bim, pca_var, by="SNP")

# Vérifier et renommer les colonnes de Position s'il y a des doublons
if ("Position.x" %in% colnames(merged_data)) {
  merged_data <- merged_data %>%
    rename(Position = Position.x) %>%
    select(-Position.y)
}

if ("Chromosome.x" %in% colnames(merged_data)) {
  merged_data <- merged_data %>%
    rename(Chromosome = Chromosome.x) %>%
    select(-Chromosome.y)
}

# Convertir en numérique
merged_data$Position <- as.numeric(merged_data$Position)
merged_data$PC2 <- as.numeric(merged_data$PC2)

# Filtrer les valeurs non valides
tidy_data <- merged_data %>%
  filter(!is.na(PC2) & PC2 > 0 & is.finite(PC2)) %>%  # Supprimer les NA, valeurs négatives et infinies
  mutate(P = pmax(0, -log10(PC2)))  # Transformer PC2 en une valeur utilisable et s'assurer qu'elle est positive

# Vérifier que les valeurs sont finies
tidy_data <- tidy_data %>% filter(is.finite(P))

# Adapter les données pour qqman
manhattan_data <- tidy_data %>%
  select(CHR = Chromosome, BP = Position, SNP, P)

manhattan_data <- manhattan_data %>% filter(P > 0)

# Générer le Manhattan plot
manhattan(manhattan_data, col = c("blue", "red"), main = "Genome-Wide PC2 Loadings", 
          xlab = "Position génomique", ylab = "-log10(PC2)")

sum(is.na(manhattan_data$P))  # Vérifie les valeurs NA
sum(is.infinite(manhattan_data$P))  # Vérifie les valeurs infinies
summary(manhattan_data$P)  # Vérifie la distribution des valeurs
