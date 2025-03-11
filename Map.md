# Map

# Installation des packages nécessaires
if (!requireNamespace("rnaturalearth", quietly = TRUE)) install.packages("rnaturalearth")
if (!requireNamespace("rnaturalearthdata", quietly = TRUE)) install.packages("rnaturalearthdata")
if (!requireNamespace("ggplot2", quietly = TRUE)) install.packages("ggplot2")
if (!requireNamespace("ggspatial", quietly = TRUE)) install.packages("ggspatial")
if (!requireNamespace("cowplot", quietly = TRUE)) install.packages("cowplot")
if (!requireNamespace("ggrepel", quietly = TRUE)) install.packages("ggrepel")

# Chargement des bibliothèques
library(ggplot2)
library(rnaturalearth)
library(rnaturalearthdata)
library(ggspatial)  # Pour ajouter l'échelle et la rose des vents
library(cowplot)    # Pour combiner les cartes
library(ggrepel)    # Pour des étiquettes non chevauchantes

# Chargement des données à partir d'un fichier CSV
data <- read.csv("Data_lobster1.csv")

# Filtrage des données pour éliminer les lignes avec des valeurs manquantes
# Cela garantit que seules les lignes avec des valeurs valides sont conservées
data <- na.omit(data[, c("Population_ID", "Lat", "Long", "Nb")])

# Chargement des données de la carte du monde
world <- ne_countries(scale = "medium", returnclass = "sf")

# Définition des limites pour zoomer sur la zone d'étude
lat_range <- range(data$Lat) + c(-1.5, 1.5)  # Ajoute une marge pour améliorer la visibilité
long_range <- range(data$Long) + c(-1.5, 1.5)

# Carte principale (zoomée)
main_map <- ggplot() +
  geom_sf(data = world, fill = "gray85", color = "white") +  # Affiche la carte de base
  coord_sf(xlim = long_range, ylim = lat_range, expand = FALSE) +  # Zoom sur la zone souhaitée
  geom_point(data = data, aes(x = Long, y = Lat, color = factor(Population_ID)), 
             size = 4, alpha = 0.8) +  # Affichage des points avec une taille fixe
  geom_text_repel(data = data, aes(x = Long, y = Lat, label = Population_ID), 
                  size = 3, fontface = "bold", color = "black", max.overlaps = Inf, 
                  box.padding = 0.5, point.padding = 0.5, segment.color = "grey50") +  # Étiquettes non chevauchantes
  theme_void() +  # Suppression des grilles et axes
  labs(title = "Population map",
       x = "Longitude",
       y = "Latitude") +
  annotation_scale(location = "bl", width_hint = 0.2, text_col = "black") +  # Ajout d'une échelle en bas à gauche
  annotation_north_arrow(location = "tl", which_north = "true",  # Ajout d'une rose des vents en haut à gauche
                         style = north_arrow_fancy_orienteering()) +
  theme(panel.background = element_rect(fill = "lightblue"),  # Couleur de la mer
        legend.position = "none")  # Suppression de la légende

# Mini-carte (planisphère)
overview_map <- ggplot() +
  geom_sf(data = world, fill = "gray85", color = "white") +  # Planisphère
  geom_rect(aes(xmin = long_range[1], xmax = long_range[2], ymin = lat_range[1], ymax = lat_range[2]),
            fill = NA, color = "red", size = 1) +  # Cadre rouge pour indiquer la zone d'étude
  theme_void() +  # Suppression des axes et grilles
  labs(title = "") +  # Pas de titre
  theme(panel.background = element_rect(fill = "lightblue"),  # Couleur de la mer
        plot.margin = margin(0, 0, 0, 0))  # Réduction des marges pour une meilleure intégration

# Superposition de la mini-carte sur la carte principale (en bas à droite)
final_map <- ggdraw() +
  draw_plot(main_map) +  # Carte principale
  draw_plot(overview_map, x = 0.75, y = 0.02, width = 0.25, height = 0.25)  # Mini-carte en bas à droite

# Affichage de la carte finale
print(final_map)
