ACP filtre colore par latitude

# Charger les bibliothèques nécessaires
library(ggplot2)
library(dplyr)
library(gridExtra)

# Charger les données ACP et profondeur de séquençage
acp <- read.table("ACP_Lobster.eigenvec", header=FALSE)
depth <- read.table("Lobster1MB.idepth", header=TRUE)

# Renommer les colonnes de l'ACP
colnames(acp) <- c("FID", "IID", paste0("PC", 1:(ncol(acp)-2)))

# Vérifier et renommer la colonne FID dans depth si nécessaire
if (!"FID" %in% colnames(depth)) {
  colnames(depth)[1] <- "FID"
}

# Supprimer la colonne IID et fusionner avec la profondeur de séquençage
acp <- acp %>% select(-IID) %>% merge(depth, by="FID")

# Vérifier la présence de la colonne MEAN_DEPTH
if (!"MEAN_DEPTH" %in% colnames(acp)) {
  stop("Erreur : La colonne MEAN_DEPTH est absente. Vérifiez le fichier Lobster1MB.idepth.")
}

# Appliquer un trimming des 5% extrêmes pour chaque PC utilisé
for (i in 1:9) {
  upper_pc <- quantile(acp[[paste0("PC", i)]], 0.95)
  acp <- acp %>% filter(acp[[paste0("PC", i)]] <= upper_pc)
}
# Charger le fichier .fam pour récupérer la latitude
fam <- read.table("Lobster1MB.fam", header=FALSE)

# Vérifier que le fichier .fam contient bien 6 colonnes
if (ncol(fam) < 6) {
  stop("Erreur : Le fichier .fam ne contient pas suffisamment de colonnes.")
}

# Renommer les colonnes du fichier .fam
colnames(fam) <- c("FID", "IID", "PID", "MID", "SEX", "OK", "LATITUDE")

# Fusionner avec le tableau ACP filtré en associant par FID
acp <- merge(acp, fam[, c("FID", "LATITUDE")], by="FID", all.x=TRUE)

# Sauvegarder le fichier filtré avec ACP et latitude
write.table(acp, file="Filtered_ACP_Lobster_with_Latitude.txt", quote=FALSE, row.names=FALSE, sep="\t")

# Définir une palette de couleurs personnalisée : Bleu → Rouge → Jaune
color_palette <- c("blue", "red", "yellow")

# Créer une liste de graphiques pour PC1 vs PC2, PC2 vs PC3, ..., PC9 vs PC10 colorés par latitude
plot_list <- list()
for (i in 1:9) {
  p <- ggplot(acp, aes_string(x=paste0("PC", i), y=paste0("PC", i+1), color="LATITUDE")) +
    geom_point(size=2) +
    scale_color_gradientn(colors = color_palette) +
    theme_minimal() +
    labs(title=paste0("PC", i, " vs PC", i+1),
         x=paste0("PC", i), 
         y=paste0("PC", i+1),
         color="Latitude")
  
  plot_list[[i]] <- p
}

# Afficher tous les graphes dans une grille (3 colonnes)
do.call(grid.arrange, c(plot_list, ncol=3))
