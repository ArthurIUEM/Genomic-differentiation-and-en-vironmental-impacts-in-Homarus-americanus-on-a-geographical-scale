
# selection des outliers pour les differentes PC
pc1 <- read.table("PC1_assoc.PC1.glm.linear", header = TRUE)
colnames(pc1) <- c("CHROM", "POS", "ID", "REF", "ALT", "PROVISIONAL_REF", "A1", "OMITTEDA1_FREQ", 
                     "TEST", "OBS_CT", "BETA", "SE", "T_STAT", "P", "ERRCODE")
outliers_pc1 <- pc1[pc1$P < 1e-5, "ID"]
write.table(outliers_pc1, "snps_PC1.txt", row.names = FALSE, col.names = FALSE, quote = FALSE)
pc2 <- read.table("PC2_assoc.PC2.glm.linear", header = TRUE)
colnames(pc2) <- c("CHROM", "POS", "ID", "REF", "ALT", "PROVISIONAL_REF", "A1", "OMITTEDA1_FREQ", 
                     "TEST", "OBS_CT", "BETA", "SE", "T_STAT", "P", "ERRCODE")
outliers_pc2 <- pc2[pc2$P < 1e-5, "ID"]
write.table(outliers_pc2, "snps_PC2.txt", row.names = FALSE, col.names = FALSE, quote = FALSE)
pc3 <- read.table("PC3_assoc.PC3.glm.linear", header = TRUE)
colnames(pc3) <- c("CHROM", "POS", "ID", "REF", "ALT", "PROVISIONAL_REF", "A1", "OMITTEDA1_FREQ", 
                     "TEST", "OBS_CT", "BETA", "SE", "T_STAT", "P", "ERRCODE")
outliers_pc3 <- pc3[pc3$P < 1e-5, "ID"]
write.table(outliers_pc3, "snps_PC3.txt", row.names = FALSE, col.names = FALSE, quote = FALSE)
pc4 <- read.table("PC4_assoc.PC4.glm.linear", header = TRUE)
colnames(pc4) <- c("CHROM", "POS", "ID", "REF", "ALT", "PROVISIONAL_REF", "A1", "OMITTEDA1_FREQ", 
                     "TEST", "OBS_CT", "BETA", "SE", "T_STAT", "P", "ERRCODE")
outliers_pc4 <- pc4[pc4$P < 1e-5, "ID"]
write.table(outliers_pc4, "snps_PC4.txt", row.names = FALSE, col.names = FALSE, quote = FALSE)

library(data.table)
library(vegan)

run_rda <- function(raw_prefix, env_data, pc_label) {
  message(paste0("\n🔎 Analyse RDA pour ", pc_label))
  
  # Lire le fichier .raw
  raw_file <- paste0(raw_prefix, ".raw")
  geno <- fread(raw_file)
  
  # Nettoyage initial (supprimer colonnes inutiles)
  geno <- geno[, !c("FID", "MAT", "PAT", "SEX", "PHENOTYPE"), with = FALSE]
  setnames(geno, "IID", "ID")
  setnames(env_data, "FID", "ID")
  
  # Identifier les individus communs
  common_ids <- intersect(geno$ID, env_data$ID)
  geno <- geno[ID %in% common_ids]
  env_filtered <- env_data[ID %in% common_ids]
  
  # Réalignement
  setkey(geno, ID)
  setkey(env_filtered, ID)
  
  # Supprimer lignes avec NA dans env_filtered (après réalignement)
  env_filtered <- na.omit(env_filtered)
  geno <- geno[ID %in% env_filtered$ID]
  
  # Retirer colonne ID
  geno_final <- geno[, !"ID"]
  env_final <- env_filtered[, !"ID"]
  
  # Conversion forcée en numérique si nécessaire
  non_numeric <- names(geno_final)[!sapply(geno_final, is.numeric)]
  if (length(non_numeric) > 0) {
    message("🔧 Colonnes non numériques détectées : ", paste(non_numeric, collapse = ", "))
    message("➡️  Conversion forcée en numérique...")
    geno_final <- geno_final[, lapply(.SD, function(x) suppressWarnings(as.numeric(as.character(x))))]
  }
  
  # Supprimer colonnes avec NA
  nb_na_cols <- sum(colSums(is.na(geno_final)) > 0)
  if (nb_na_cols > 0) {
    message("❗ ", nb_na_cols, " colonnes génotypiques contiennent encore des NAs après conversion. Elles vont être supprimées.")
    geno_final <- geno_final[, which(colSums(is.na(geno_final)) == 0), with = FALSE]
  }
  
  # Supprimer SNPs avec >5% de NAs
  message("🧹 Suppression des SNPs avec plus de 5% de valeurs manquantes...")
  geno_final <- geno_final[, which(colMeans(is.na(geno_final)) < 0.05), with = FALSE]
  
  # Imputation des NA restants
  if (any(is.na(geno_final))) {
    message("🧩 Imputation des valeurs manquantes restantes par la moyenne...")
    geno_final <- geno_final[, lapply(.SD, function(x) ifelse(is.na(x), mean(x, na.rm = TRUE), x))]
  }
  
  # Garder uniquement colonnes numériques dans env_final
  env_final <- env_final[, which(sapply(env_final, is.numeric)), with = FALSE]
  
  # Supprimer colonnes constantes ou entièrement NA
  env_final <- env_final[, which(colSums(is.na(env_final)) < nrow(env_final)), with = FALSE]
  env_final <- env_final[, which(sapply(env_final, sd, na.rm = TRUE) > 0), with = FALSE]
  
  # Centrage et réduction
  message("⚖️  Centrages et réductions des variables environnementales...")
  env_final <- as.data.table(scale(env_final))
  
  # Vérification finale
  if (any(is.na(geno_final))) stop("❌ Des NA sont encore présents dans les données génotypiques.")
  if (any(is.na(env_final))) stop("❌ Des NA sont encore présents dans les données environnementales.")
  
  # Lancer la RDA
  message("🚀 Lancement de la RDA...")
  rda_model <- rda(geno_final ~ ., data = env_final)
  print(summary(rda_model))
  return(rda_model)
}

# Charger les données environnementales
# Chargement
env_data_full <- fread("Filtered_ACP_Lobster_with_Lat_Env.txt")

# Exemple : sélection manuelle des colonnes environnementales
cols_env <- c("FID", "Bathymetrie_moy", "Temperature_max", "Temperature_moy", "Temperature_min", "Dissolution_max", "Dissolution_moy", "Dissolution_min", "Salinity_max", "Salinity_moy", "Salinity_min")
env_data_clean <- env_data_full[, ..cols_env]

# Sauvegarde
fwrite(env_data_clean, "Filtered_ENV_ONLY.txt", sep = "\t")

# Sélectionner uniquement les colonnes ID et Latitude
env_latitude <- env_data_full[, .(FID, LATITUDE)]

# Vérifier les premiers éléments
head(env_latitude)


env_data <- fread("Filtered_ENV_ONLY.txt")
# Lancer les analyses RDA
rda_PC2 <- run_rda("PC2_outliers", copy(env_data), "PC2")
rda_PC3 <- run_rda("PC3_outliers", copy(env_data), "PC3")
rda_PC4 <- run_rda("PC4_outliers", copy(env_data), "PC4")

# Librairie utile
library(vegan)

# ⚠️ Choisir le modèle à visualiser
# Ex : pour PC2
rda_model <- rda_PC2
rda_label <- "PC2"

# 🎨 Couleurs et options
cols <- scales::hue_pal()(3) # palette de base

# 🖼️ Triplot global : individus + variables + SNPs
plot(rda_model, type = "n", main = paste("RDA -", rda_label))
points(rda_model, display = "sites", col = "grey30", pch = 16, cex = 0.8)
text(rda_model, display = "bp", col = "blue", cex = 1)
text(rda_model, display = "species", col = "red", cex = 0.6)

plot(rda_model, type = "n", main = paste("RDA - Indiv -", rda_label))
points(rda_model, display = "sites", col = "black", pch = 16, cex = 0.8)

plot(rda_model, type = "n", main = paste("RDA - Environnement -", rda_label))
text(rda_model, display = "bp", col = "blue", cex = 1)

plot(rda_model, type = "n", main = paste("RDA - SNPs -", rda_label))
text(rda_model, display = "species", col = "red", cex = 0.5)

# ⚠️ Choisir le modèle à visualiser
# Ex : pour PC3
rda_model <- rda_PC3
rda_label <- "PC3"

# 🎨 Couleurs et options
cols <- scales::hue_pal()(3) # palette de base

# 🖼️ Triplot global : individus + variables + SNPs
plot(rda_model, type = "n", main = paste("RDA -", rda_label))
points(rda_model, display = "sites", col = "grey30", pch = 16, cex = 0.8)
text(rda_model, display = "bp", col = "blue", cex = 1)
text(rda_model, display = "species", col = "red", cex = 0.6)

plot(rda_model, type = "n", main = paste("RDA - Indiv -", rda_label))
points(rda_model, display = "sites", col = "black", pch = 16, cex = 0.8)

plot(rda_model, type = "n", main = paste("RDA - Environnement -", rda_label))
text(rda_model, display = "bp", col = "blue", cex = 1)

plot(rda_model, type = "n", main = paste("RDA - SNPs -", rda_label))
text(rda_model, display = "species", col = "red", cex = 0.5)

# ⚠️ Choisir le modèle à visualiser
# Ex : pour PC4
rda_model <- rda_PC4
rda_label <- "PC4"

# 🎨 Couleurs et options
cols <- scales::hue_pal()(3) # palette de base

# 🖼️ Triplot global : individus + variables + SNPs
plot(rda_model, type = "n", main = paste("RDA -", rda_label))
points(rda_model, display = "sites", col = "grey30", pch = 16, cex = 0.8)
text(rda_model, display = "bp", col = "blue", cex = 1)
text(rda_model, display = "species", col = "red", cex = 0.6)

plot(rda_model, type = "n", main = paste("RDA - Indiv -", rda_label))
points(rda_model, display = "sites", col = "black", pch = 16, cex = 0.8)

plot(rda_model, type = "n", main = paste("RDA - Environnement -", rda_label))
text(rda_model, display = "bp", col = "blue", cex = 1)

plot(rda_model, type = "n", main = paste("RDA - SNPs -", rda_label))
text(rda_model, display = "species", col = "red", cex = 0.5)
