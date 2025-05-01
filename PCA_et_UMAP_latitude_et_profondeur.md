# Charger les packages nécessaires
library(data.table)
library(ggplot2)
library(patchwork)
library(uwot)
library(scales)
library(viridis)

# Charger le fichier de profondeur de séquençage
depth <- fread("Lobster1MB.idepth")

# Calculer les seuils des 5% inférieurs et supérieurs
low_perc <- quantile(depth$MEAN_DEPTH, 0.05)
high_perc <- quantile(depth$MEAN_DEPTH, 0.95)

# Conserver uniquement les individus entre ces deux seuils
filtered_depth <- depth[MEAN_DEPTH > low_perc & MEAN_DEPTH < high_perc]

# Charger les résultats de la PCA filtrée
pca <- fread("Lobster_filtered_PCA.eigenvec")
colnames(pca) <- c("FID", "IID", paste0("PC", 1:(ncol(pca)-2)))

# Fusionner les données de profondeur filtrées avec la PCA
pca_filtered <- merge(pca, filtered_depth, by.x = "IID", by.y = "INDV")

# 🔴 Filtrer les individus avec PC2 > -0.4
pca_filtered <- pca_filtered[PC2 > -0.4]

# Charger le fichier .fam pour obtenir la latitude
fam <- fread("Lobster1MB.fam")
colnames(fam) <- c("FID", "IID", "Father", "Mother", "Sex", "Phenotype", "Latitude", "Longitude", "NAFO Zone")

# Fusionner la latitude avec les données PCA filtrées
pca_filtered <- merge(pca_filtered, fam[, c("IID", "Latitude")], by = "IID")

# Appliquer UMAP sur les 10 premières composantes principales
umap_res <- umap(pca_filtered[, paste0("PC", 1:10), with = FALSE], n_neighbors = 15, min_dist = 0.1)
pca_filtered$UMAP1 <- umap_res[, 1]
pca_filtered$UMAP2 <- umap_res[, 2]

# Définir une palette de couleurs personnalisée pour la latitude
color_palette <- c("red", "orange", "yellow", "green", "blue")

# Créer une échelle continue personnalisée pour la latitude (en discret ordonné)
p1 <- ggplot(pca_filtered, aes(PC1, PC2, color = Latitude)) + 
  geom_point(alpha = 0.7) + ggtitle("PC1 vs PC2") + theme_minimal() + 
  scale_color_gradientn(colors = color_palette)
p2 <- ggplot(pca_filtered, aes(PC2, PC3, color = Latitude)) + 
  geom_point(alpha = 0.7) + ggtitle("PC2 vs PC3") + theme_minimal() + 
  scale_color_gradientn(colors = color_palette)
p3 <- ggplot(pca_filtered, aes(PC3, PC4, color = Latitude)) + 
  geom_point(alpha = 0.7) + ggtitle("PC3 vs PC4") + theme_minimal() + 
  scale_color_gradientn(colors = color_palette)
p4 <- ggplot(pca_filtered, aes(UMAP1, UMAP2, color = Latitude)) + 
  geom_point(alpha = 0.7) + ggtitle("UMAP1 vs UMAP2") + theme_minimal() + 
  scale_color_gradientn(colors = color_palette)

# Utiliser une palette continue viridis 'magma' pour la profondeur
p5 <- ggplot(pca_filtered, aes(UMAP1, UMAP2, color = MEAN_DEPTH)) + 
  geom_point(alpha = 0.7) + ggtitle("UMAP1 vs UMAP2") + theme_minimal() + 
  scale_color_viridis_c(option = "magma")
p6 <- ggplot(pca_filtered, aes(PC1, UMAP2, color = MEAN_DEPTH)) + 
  geom_point(alpha = 0.7) + ggtitle("PC1 vs UMAP2") + theme_minimal() + 
  scale_color_viridis_c(option = "magma")

# Affichage combiné
(p1 | p2 | p3) / (p4 | p5 | p6)

# Thème commun pour agrandir textes et légendes
theme_big <- theme(
  plot.title = element_text(size = 17, face = "bold"),
  legend.title = element_text(size = 15),
  legend.text = element_text(size = 13),
  axis.title = element_text(size = 15),
  axis.text = element_text(size = 13)
)

# Appliquer le thème à chaque graphique
p1 <- p1 + theme_big
p2 <- p2 + theme_big
p3 <- p3 + theme_big
p4 <- p4 + theme_big
p5 <- p5 + theme_big
p6 <- p6 + theme_big

# Panel 1 : PCA + UMAP colorés par latitude
panel_lat <- (p1 | p2) / (p3 | p4) + 
  plot_annotation(
    title = "PCA and UMAP colored by latitude without sex chromosome",
    theme = theme(
      plot.title = element_text(size = 20, face = "bold", hjust = 0.5)
    )
  )

# Panel 2 : UMAP coloré par profondeur
panel_depth <- (p5 | p6) + 
  plot_annotation(
    title = "UMAP and UMAP2 VS PC1 colored by sequencing depth without sex chromosome",
    theme = theme(
      plot.title = element_text(size = 20, face = "bold", hjust = 0.5)
    )
  )

# Afficher les deux panels séparément
panel_lat
panel_depth
