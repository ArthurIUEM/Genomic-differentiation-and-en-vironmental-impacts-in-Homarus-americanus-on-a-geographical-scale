
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
library(scales)
library(viridis)

run_rda <- function(raw_prefix, env_data, pc_label) {
  message(paste0("\n🔎 Analyse RDA pour ", pc_label))
  
  raw_file <- paste0(raw_prefix, ".raw")
  geno <- fread(raw_file)
  
  geno <- geno[, !c("FID", "MAT", "PAT", "SEX", "PHENOTYPE"), with = FALSE]
  setnames(geno, "IID", "ID")
  setnames(env_data, "FID", "ID")
  
  common_ids <- intersect(geno$ID, env_data$ID)
  geno <- geno[ID %in% common_ids]
  env_filtered <- env_data[ID %in% common_ids]
  
  setkey(geno, ID)
  setkey(env_filtered, ID)
  
  env_filtered <- na.omit(env_filtered)
  geno <- geno[ID %in% env_filtered$ID]
  
  geno_final <- geno[, !"ID"]
  env_final <- env_filtered[, !"ID"]
  
  non_numeric <- names(geno_final)[!sapply(geno_final, is.numeric)]
  if (length(non_numeric) > 0) {
    message("🔧 Colonnes non numériques détectées : ", paste(non_numeric, collapse = ", "))
    message("➡️  Conversion forcée en numérique...")
    geno_final <- geno_final[, lapply(.SD, function(x) suppressWarnings(as.numeric(as.character(x))))]
  }
  
  nb_na_cols <- sum(colSums(is.na(geno_final)) > 0)
  if (nb_na_cols > 0) {
    message("❗ ", nb_na_cols, " colonnes génotypiques contiennent encore des NAs après conversion. Elles vont être supprimées.")
    geno_final <- geno_final[, which(colSums(is.na(geno_final)) == 0), with = FALSE]
  }
  
  message("🧹 Suppression des SNPs avec plus de 5% de valeurs manquantes...")
  geno_final <- geno_final[, which(colMeans(is.na(geno_final)) < 0.05), with = FALSE]
  
  if (any(is.na(geno_final))) {
    message("🧩 Imputation des valeurs manquantes restantes par la moyenne...")
    geno_final <- geno_final[, lapply(.SD, function(x) ifelse(is.na(x), mean(x, na.rm = TRUE), x))]
  }
  
  env_final <- env_final[, which(sapply(env_final, is.numeric)), with = FALSE]
  env_final <- env_final[, which(colSums(is.na(env_final)) < nrow(env_final)), with = FALSE]
  env_final <- env_final[, which(sapply(env_final, sd, na.rm = TRUE) > 0), with = FALSE]
  
  message("⚖️  Centrages et réductions des variables environnementales...")
  env_final <- as.data.table(scale(env_final))
  
  if (any(is.na(geno_final))) stop("❌ Des NA sont encore présents dans les données génotypiques.")
  if (any(is.na(env_final))) stop("❌ Des NA sont encore présents dans les données environnementales.")
  
  message("🚀 Lancement de la RDA...")
  rda_model <- rda(geno_final ~ ., data = env_final)
  print(summary(rda_model))
  return(rda_model)
}

env_data_full <- fread("Filtered_ACP_Lobster_with_Lat_Env.txt")

cols_env <- c("FID", "Bathymetrie_moy", "Temperature_max", "Temperature_moy", "Temperature_min",
              "Dissolution_max", "Dissolution_moy", "Dissolution_min",
              "Salinity_max", "Salinity_moy", "Salinity_min")
env_data_clean <- env_data_full[, ..cols_env]
fwrite(env_data_clean, "Filtered_ENV_ONLY.txt", sep = "\t")

# Latitude
env_latitude <- env_data_full[, .(FID, LATITUDE)]
setnames(env_latitude, "FID", "ID")

env_data <- fread("Filtered_ENV_ONLY.txt")
rda_PC2 <- run_rda("PC2_outliers", copy(env_data), "PC2")
rda_PC3 <- run_rda("PC3_outliers", copy(env_data), "PC3")
rda_PC4 <- run_rda("PC4_outliers", copy(env_data), "PC4")

library(ggplot2)
library(data.table)
library(viridis)

plot_rda_sites_colored_by_latitude <- function(rda_model, rda_label, env_latitude) {
  # Récupérer les scores des individus
  site_scores <- as.data.frame(scores(rda_model, display = "sites", choices = 1:2))  # RDA1 & RDA2
  site_scores$ID <- rownames(site_scores)
  
  # Fusion avec les latitudes
  site_lat <- merge(site_scores, env_latitude, by = "ID")
  
  # Plot avec ggplot
  ggplot(site_lat, aes(x = RDA1, y = RDA2, color = LATITUDE)) +
    geom_point(size = 2.5) +
    scale_color_viridis(option = "viridis", name = "Latitude", direction = -1) +
    theme_minimal(base_size = 14) +
    labs(
      title = paste("RDA -", rda_label),
      x = "RDA1",
      y = "RDA2"
    ) +
    theme(legend.position = "right")
}

plot_rda_sites_colored_by_latitude(rda_PC2, "PC2", env_latitude)
plot_rda_sites_colored_by_latitude(rda_PC3, "PC3", env_latitude)
plot_rda_sites_colored_by_latitude(rda_PC4, "PC4", env_latitude)

