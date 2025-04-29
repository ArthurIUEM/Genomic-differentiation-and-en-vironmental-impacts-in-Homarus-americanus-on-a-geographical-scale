# Charger les packages
```
library(ggplot2)
library(patchwork)
```
# Charger les données
```
df <- read.table("UMAP_with_clusters_trimmed.txt", header = TRUE, sep = "\t")
df$LATITUDE <- as.numeric(as.character(df$LATITUDE))
```
# Palette de couleurs
```
color_palette <- c("red", "orange", "yellow", "green", "blue")
```
# Variance expliquée (à adapter avec tes vraies valeurs)
```
variance_explained <- c(30.2, 25.6, 15.3, 10.1)  # En %
```
# Thème commun avec textes agrandis
```
custom_theme <- theme_minimal() +
  theme(
    plot.title = element_text(size = 22, face = "bold"),
    axis.title = element_text(size = 18),
    axis.text = element_text(size = 14),
    legend.title = element_text(size = 16),
    legend.text = element_text(size = 14)
  )
```
# Limites coordonnées
```
xlims <- range(c(df$PC1, df$PC2, df$PC3, df$PC4), na.rm = TRUE)
ylims <- xlims
```
# Graphiques
```
p1 <- ggplot(df, aes(x = PC1, y = PC2, color = LATITUDE)) +
  geom_point(size = 2.5, alpha = 0.8) +
  scale_color_gradientn(colors = color_palette) +
  labs(
    title = paste0("PC1 vs PC2"),
    color = "Latitude", x = "PC1 (30.2%)", y = "PC2 (25.6%)"
  ) +
  custom_theme +
  coord_fixed() +
  xlim(xlims) + ylim(ylims)

p2 <- ggplot(df, aes(x = PC2, y = PC3, color = LATITUDE)) +
  geom_point(size = 2.5, alpha = 0.8) +
  scale_color_gradientn(colors = color_palette) +
  labs(
    title = paste0("PC2 vs PC3"),
    color = "Latitude", x = "PC2 (25.6%)", y = "PC3 (15.3%)"
  ) +
  custom_theme +
  coord_fixed() +
  xlim(xlims) + ylim(ylims)

p3 <- ggplot(df, aes(x = PC3, y = PC4, color = LATITUDE)) +
  geom_point(size = 2.5, alpha = 0.8) +
  scale_color_gradientn(colors = color_palette) +
  labs(
    title = paste0("PC3 vs PC4"),
    color = "Latitude", x = "PC3 (15.3%)", y = "PC4 (10.1%)"
  ) +
  custom_theme +
  coord_fixed() +
  xlim(xlims) + ylim(ylims)
```
# Combiner les graphiques avec une seule légende
```
panel <- (p1 | p2 | p3) +
  plot_layout(guides = "collect") +  # collecte les légendes
  plot_annotation(
    title = "PCA colored by latitude after bias correction",
    theme = theme(
      plot.title = element_text(size = 26, face = "bold", hjust = 0.5)
    )
  ) &
  theme(legend.position = "right")  # positionne la légende à droite

print(panel)
```
