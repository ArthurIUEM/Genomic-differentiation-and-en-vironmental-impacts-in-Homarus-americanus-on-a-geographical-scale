library(ggplot2)

# Charger les données
data <- read.table("PCA_UMAP_Lobster_with_Clusters.txt", header = TRUE, sep = "\t", stringsAsFactors = FALSE)

# Vérifier les colonnes disponibles
if (!all(c("UMAP1", "UMAP2", "LATITUDE") %in% colnames(data))) {
  stop("Les colonnes UMAP1, UMAP2 ou LATITUDE sont absentes du fichier.")
}

# Conversion de la latitude en facteur si nécessaire
data$LATITUDE <- as.numeric(data$LATITUDE)

ggplot(data, aes(x = UMAP1, y = UMAP2, color = LATITUDE)) +
  geom_point(size = 2, alpha = 0.7) +
  scale_color_viridis_c(option = "plasma") +
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
