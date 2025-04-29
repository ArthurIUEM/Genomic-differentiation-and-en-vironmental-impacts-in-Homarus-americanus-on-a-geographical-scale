library(ggplot2)

# Charger les données
data <- read.table("UMAP_with_clusters_assigned.txt", header = TRUE, sep = "\t", stringsAsFactors = FALSE)

# Vérifier les colonnes disponibles
if (!all(c("UMAP1", "UMAP2", "LATITUDE") %in% colnames(data))) {
  stop("Les colonnes UMAP1, UMAP2 ou LATITUDE sont absentes du fichier.")
}

# Conversion de la latitude
data$LATITUDE <- as.numeric(data$LATITUDE)

# Palette de couleurs personnalisée
color_palette <- c("red", "orange", "yellow", "green", "cyan", "blue")

# Graphique
ggplot(data, aes(x = UMAP1, y = UMAP2, color = LATITUDE)) +
  geom_point(size = 2, alpha = 0.7) +
  scale_color_gradientn(colors = color_palette) +
  labs(
    title = "Projection UMAP colorée par la latitude",
    x = "UMAP1",
    y = "UMAP2",
    color = "Latitude"
  ) +
  theme_minimal() +
  theme(
    plot.title = element_text(size = 20, face = "bold"),
    axis.title = element_text(size = 16),
    axis.text = element_text(size = 14),
    legend.title = element_text(size = 16),
    legend.text = element_text(size = 14)
  )
