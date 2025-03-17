Manhattan plot

# Charger les librairies
library(ggplot2)
library(qqman)
library(dplyr)
library(data.table)

# Charger le fichier des SNPs
bim <- fread("Lobster1MB.bim", header = FALSE)
colnames(bim) <- c("CHR", "SNP", "CM", "BP", "A1", "A2")

# Charger les PC loadings
pc_loading <- fread("PCA_results.eigenvec.var", header = TRUE)
colnames(pc_loading) <- c("CHR", "SNP", "MAJ", "NONMAJ", paste0("PC", 1:10))

# Boucle pour PC1 à PC4
for (pc in 1:4) {
  
  pc_col <- paste0("PC", pc)  # Nom de la colonne du PC
  message("\n🔍 Génération des Manhattan plots pour ", pc_col, "...\n")
  
  # Sélectionner le PC correspondant (valeurs brutes)
  pc_data <- pc_loading[, .(CHR, SNP, PC = get(pc_col))]  
  
  # Fusionner avec les positions des SNPs
  snp_data <- merge(bim, pc_data, by = c("SNP", "CHR"))
  
  # ✅ Conversion des chromosomes en valeurs numériques
  snp_data$CHR_numeric <- as.numeric(factor(snp_data$CHR, levels = unique(snp_data$CHR)))
  
  # --- 📊 Manhattan plot SANS transformation logarithmique ---
  min_pc <- min(snp_data$PC, na.rm = TRUE)
  max_pc <- max(snp_data$PC, na.rm = TRUE)
  
  png(paste0(pc_col, "_Manhattan_Raw.png"), width = 1200, height = 600)
  manhattan(snp_data, chr="CHR_numeric", bp="BP", p="PC", snp="SNP", 
            col=c("blue4", "orange3"), ylim=c(min_pc, max_pc))  
  title(main = paste("Manhattan Plot -", pc_col, "(Valeurs brutes)"))
  dev.off()
  
  message("✅ Manhattan plot sans transformation pour ", pc_col, " généré avec succès !\n")
  
  # --- 📊 Manhattan plot AVEC transformation logarithmique ---
  snp_data[, logPC := -log10(abs(PC) + 1e-10)]  # Transformation
  
  min_logPC <- min(snp_data$logPC, na.rm = TRUE)
  max_logPC <- max(snp_data$logPC, na.rm = TRUE)
  
  png(paste0(pc_col, "_Manhattan_Log.png"), width = 1200, height = 600)
  manhattan(snp_data, chr="CHR_numeric", bp="BP", p="logPC", snp="SNP", 
            col=c("blue4", "orange3"), ylim=c(min_logPC, max_logPC))  
  title(main = paste("Manhattan Plot -", pc_col, "(Transformation log)"))
  dev.off()
  
  message("✅ Manhattan plot avec transformation log pour ", pc_col, " généré avec succès !\n")
}



