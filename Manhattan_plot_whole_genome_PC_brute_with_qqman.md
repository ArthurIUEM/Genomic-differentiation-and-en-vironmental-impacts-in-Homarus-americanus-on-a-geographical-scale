library(qqman)
library(dplyr)

# Charger les fichiers
bim <- read.table("Lobster1MB.bim", header = FALSE, stringsAsFactors = FALSE)
colnames(bim) <- c("CHR", "SNP", "CM", "BP", "A1", "A2")

pca_var <- read.table("PCA_results.eigenvec.var", header = TRUE, stringsAsFactors = FALSE)
colnames(pca_var) <- c("CHR", "SNP", "A1", "A2", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")

# Vérifier la structure des fichiers
head(bim)
head(pca_var)

# Fusionner les fichiers sur le SNP ID
merged_data <- merge(bim, pca_var, by.x = "SNP", by.y = "SNP")

# Sélectionner les colonnes nécessaires
manhattan_data <- merged_data %>% select(CHR.x, BP, PC2, SNP)

# Convertir en valeurs absolues si nécessaire
# Convertir PC2loading en numérique
manhattan_data$PC2 <- as.numeric(manhattan_data$PC2)

# Vérifier s'il y a des valeurs non convertibles
sum(is.na(manhattan_data$PC2))  # Doit être 0 si tout est bien converti

# Appliquer abs() après conversion
manhattan_data$PC2 <- abs(manhattan_data$PC2)

# Nettoyer la colonne CHR en supprimant 'NW_' au début et '.1' à la fin
manhattan_data$CHR.x <- gsub("^NW_", "", manhattan_data$CHR.x)  # Supprime 'NW_' au début
manhattan_data$CHR.x <- gsub("\\.1$", "", manhattan_data$CHR.x)  # Supprime '.1' à la fin

# Convertir en numérique (si possible)
manhattan_data$CHR.x <- as.numeric(manhattan_data$CHR.x)

# Vérifier le résultat
unique(manhattan_data$CHR.x)

# Générer le Manhattan plot
manhattan(manhattan_data, chr="CHR.x", bp="BP", p="PC2", logp=FALSE, ylab="PC2", main="Manhattan Plot - PC2")


colnames(bim)
colnames(pca_var)
summary(merged_data$PC2loading)
manhattan_data$CHR <- as.numeric(manhattan_data$CHR)
manhattan_data$BP <- as.numeric(manhattan_data$BP)
manhattan_data$PC2 <- as.numeric(manhattan_data$PC2)
sum(is.na(manhattan_data$CHR))  # Doit être 0
sum(is.na(manhattan_data$BP))   # Doit être 0
sum(is.na(manhattan_data$PC2))  # Doit être 0
