install.packages(c("leaflet", "dplyr", "tidyr", "magrittr", "png", "grid", "ggplot2"))

library(leaflet)
library(dplyr)
library(tidyr)
library(ggplot2)
library(png)
library(grid)

# Données
data <- data.frame(
  Latitude = c(41.5546, 42.1896, 42.4381, 43.58317, 44.1156, 44.19267, 44.53572, 44.97435, 45.69417, 45.9175,
               46.16667, 46.23333, 46.46633, 46.86667, 47.4837, 47.55094, 47.59954, 47.70441, 48.47, 49.409, 50.6325),
  Longitude = c(-66.4178, -66.1345, -66.0812, -65.79767, -66.3, -66.5, -63.53317, -62.03392, -60.25967, -60.87333,
                -63.4325, -62.33639, -60.44522, -64.81444, -55.803, -52.9317, -52.90283, -53.00188, -64.0, -54.853, -57.3421),
  Cluster1 = c(5,3,9,20,3,22,13,12,11,12,0,0,0,0,0,0,0,0,0,0,0),
  Cluster4 = c(26,42,62,12,5,13,24,15,18,17,11,14,30,23,18,11,14,36,17,25,23),
  Cluster2 = c(0,0,0,0,0,0,3,8,3,6,9,15,2,16,13,1,1,2,21,5,26),
  Cluster3 = c(0,0,0,0,0,0,1,2,1,2,6,13,5,9,17,1,2,2,3,15,23)
)

# Fonctions pour générer une icône de camembert
generate_pie_icon <- function(clusters, lat, lng, id) {
  df <- data.frame(
    cluster = factor(names(clusters), levels = names(clusters)),
    value = as.numeric(clusters)
  ) %>% filter(value > 0)
  
  if (nrow(df) <= 1) return(NULL) # on ne génère pas d'image si un seul cluster
  
  p <- ggplot(df, aes(x = "", y = value, fill = cluster)) +
    geom_bar(stat = "identity", width = 1) +
    coord_polar("y") +
    theme_void() +
    scale_fill_manual(values = c("Cluster1" = "red", "Cluster2" = "blue", "Cluster3" = "green", "Cluster4" = "orange"))
  
  img_file <- paste0("pie", id, ".png")
  ggsave(img_file, plot = p, width = 2, height = 2, bg = "transparent")
  return(img_file)
}

# Carte leaflet
m <- leaflet() %>%
  addTiles()

# Boucle sur chaque ligne
for (i in 1:nrow(data)) {
  row <- data[i, ]
  clusters <- row %>% select(starts_with("Cluster"))
  non_zero <- clusters[clusters > 0]
  
  if (length(non_zero) == 1) {
    # Un seul cluster → point coloré
    color_map <- c("Cluster1" = "red", "Cluster2" = "blue", "Cluster3" = "green", "Cluster4" = "orange")
    cluster_name <- names(non_zero)
    m <- m %>% addCircleMarkers(
      lng = row$Longitude,
      lat = row$Latitude,
      radius = 6,
      color = color_map[cluster_name],
      label = paste(cluster_name, ":", non_zero)
    )
  } else {
    # Plusieurs clusters → camembert
    pie_file <- generate_pie_icon(clusters, row$Latitude, row$Longitude, i)
    if (!is.null(pie_file)) {
      icon <- icons(
        iconUrl = pie_file,
        iconWidth = 40, iconHeight = 40
      )
      m <- m %>% addMarkers(
        lng = row$Longitude,
        lat = row$Latitude,
        icon = icon
      )
    }
  }
}

# Légende HTML à insérer en haut à droite
legend_html <- "
<div style='padding:10px; background:white; border:2px solid #aaa; border-radius:5px;'>
  <h4 style='margin:0; font-size:16px;'>Clusters</h4>
  <div><span style='display:inline-block;width:15px;height:15px;background:red;margin-right:5px;'></span>Cluster 1</div>
  <div><span style='display:inline-block;width:15px;height:15px;background:orange;margin-right:5px;'></span>Cluster 4</div>
  <div><span style='display:inline-block;width:15px;height:15px;background:blue;margin-right:5px;'></span>Cluster 2</div>
  <div><span style='display:inline-block;width:15px;height:15px;background:green;margin-right:5px;'></span>Cluster 3</div>
</div>
"

# Ajout de la légende à la carte
m <- m %>% addControl(html = legend_html, position = "topright")

# Afficher la carte
m
