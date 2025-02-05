# Map

### Install the necessary packages
```
if (!requireNamespace("rnaturalearth", quietly = TRUE)) install.packages("rnaturalearth")
if (!requireNamespace("rnaturalearthdata", quietly = TRUE)) install.packages("rnaturalearthdata")
if (!requireNamespace("ggplot2", quietly = TRUE)) install.packages("ggplot2")
if (!requireNamespace("ggspatial", quietly = TRUE)) install.packages("ggspatial")
if (!requireNamespace("cowplot", quietly = TRUE)) install.packages("cowplot")
if (!requireNamespace("ggrepel", quietly = TRUE)) install.packages("ggrepel")
```
### Load libraries
```
library(ggplot2)
library(rnaturalearth)
library(rnaturalearthdata)
library(ggspatial)  # To add the scale and compass rose
library(cowplot)    # To combine cards
library(ggrepel)    # For non-overlapping labels
```
### Loading data from a CSV file
```
data <- read.csv("Data_lobster1.csv")
```
### Filter data to eliminate rows with missing values in required columns
```
data <- na.omit(data[, c("Population_ID", "Lat", "Long", "Nb")])
```
### Load world map data
```
world <- ne_countries(scale = "medium", returnclass = "sf")
```
### Set limits to zoom in on the study area
```
lat_range <- range(data$Lat) + c(-1.5, 1.5)  # Adds a margin
long_range <- range(data$Long) + c(-1.5, 1.5)
```
### Main map (zoomed in)
```
main_map <- ggplot() +
  geom_sf(data = world, fill = "gray85", color = "white") +  # Displays the base map
  coord_sf(xlim = long_range, ylim = lat_range, expand = FALSE) +  # Zoom in on the desired area
  geom_point(data = data, aes(x = Long, y = Lat, color = factor(Population_ID)), size = 4, alpha = 0.8) +  # Points with a fixed size
  geom_text_repel(data = data, aes(x = Long, y = Lat, label = Population_ID), 
                  size = 3, fontface = "bold", color = "black", max.overlaps = Inf, 
                  box.padding = 0.5, point.padding = 0.5, segment.color = "grey50") +  # Non-overlapping population labels
  theme_void() +  # Remove grid and axes
  labs(title = "Population map",
       x = "Longitude",
       y = "Latitude") +
  annotation_scale(location = "bl", width_hint = 0.2, text_col = "black") +  # Add a scale at bottom left
  annotation_north_arrow(location = "tl", which_north = "true",  # Add a compass rose at top left
                         style = north_arrow_fancy_orienteering()) +
  theme(panel.background = element_rect(fill = "lightblue"),  # Coulor of the sea
        legend.position = "none")  # Remove legend
```
### Mini map (planisphere)
```
overview_map <- ggplot() +
  geom_sf(data = world, fill = "gray85", color = "white") +  # Planisphere
  geom_rect(aes(xmin = long_range[1], xmax = long_range[2], ymin = lat_range[1], ymax = lat_range[2]),
            fill = NA, color = "red", size = 1) +  # Red frame to show the position of the study area
  theme_void() +  # Deleting axes and grids
  labs(title = "") +  # No title
  theme(panel.background = element_rect(fill = "lightblue"),  # Color of the sea
        plot.margin = margin(0, 0, 0, 0))  # Reduced margins for integration
```
### Overlay the mini-map on the main map (bottom right)
```
final_map <- ggdraw() +
  draw_plot(main_map) +  # Main map
  draw_plot(overview_map, x = 0.75, y = 0.02, width = 0.25, height = 0.25)  # Mini-map lower right
```
### Display final map
```
print(final_map)
```
