library(tidyverse)
library(readxl)
library(maxnet)

# Charger les fichiers
env_data <- read_delim("Filtered_ACP_Lobster_with_Lat_Env.txt", delim = "\t")
umap_data <- read_delim("UMAP_with_clusters_assigned.txt", delim = "\t")
meta_data <- read_excel("Merged_Lobster_Data.xlsx")

# Nettoyer et fusionner
meta_data <- meta_data %>%
  rename(FID = `Sample ID 2`) %>%
  select(FID, Longitude = Long.x, Year = Année)

full_data <- umap_data %>%
  left_join(env_data, by = "FID") %>%
  left_join(meta_data, by = "FID") %>%
  filter(!is.na(Longitude), !is.na(LATITUDE.x), !is.na(Cluster))

# Ajouter une variable binaire pour SDM : Sud = 1 (clusters 1 & 2), Nord = 0 (clusters 3 & 4)
full_data <- full_data %>%
  mutate(Region = ifelse(Cluster %in% c(1, 2), "Sud", "Nord"),
         Presence = ifelse(Region == "Sud", 1, 0))

# Sélection des colonnes utiles
sdm_data <- full_data %>%
  select(Presence, Longitude, LATITUDE.x, Temperature_moy, Salinity_moy, Dissolution_moy) %>%
  drop_na()

# Supprimer les colonnes sans variation (facultatif si nécessaire)
env_vars <- sdm_data %>% select(Temperature_moy, Salinity_moy, Dissolution_moy)

model_sdm <- maxnet(p = sdm_data$Presence,
                    data = env_vars,
                    f = maxnet.formula(sdm_data$Presence, env_vars, classes = "lq"))

sdm_data$Prediction <- predict(model_sdm, newdata = env_vars, type = "cloglog")

ggplot(sdm_data, aes(x = Longitude, y = LATITUDE, color = Prediction)) +
  geom_point(size = 2) +
  scale_color_viridis_c() +
  theme_minimal() +
  labs(title = "SDM - Probabilité d'appartenance au groupe Sud (Clusters 1 & 2)",
       color = "Probabilité")

pred <- predict(model_sdm, newdata = env_vars, type = "cloglog")
str(pred)
str(env_vars)
summary(env_vars)
# Retire les colonnes dont l’écart-type est trop faible
env_vars_filtered <- env_vars %>%
  select(where(~ sd(.x, na.rm = TRUE) > 1e-6))
model_sdm <- maxnet(
  p = sdm_data$Presence,
  data = env_vars_filtered,
  f = maxnet.formula(sdm_data$Presence, env_vars_filtered, classes = "lq")
)
pred <- predict(model_sdm, newdata = env_vars_filtered, type = "cloglog")
sdm_data$Prediction <- if (is.matrix(pred)) pred[,1] else pred
# Fusion dans un seul objet cohérent
sdm_ready <- sdm_data %>%
  mutate(across(c(Temperature_moy, Salinity_moy, Dissolution_moy), as.numeric)) %>%
  select(Presence, Longitude, LATITUDE.x, Temperature_moy, Salinity_moy, Dissolution_moy) %>%
  drop_na()
# Extraire les variables environnementales
env_vars <- sdm_ready %>%
  select(Temperature_moy, Salinity_moy, Dissolution_moy)

# Supprimer les colonnes avec trop peu de variance
env_vars_filtered <- env_vars %>%
  select(where(~ sd(.x, na.rm = TRUE) > 1e-6))

# Mettre à jour les présences (alignement parfait avec env_vars_filtered)
presences <- sdm_ready$Presence
model_sdm <- maxnet(
  p = presences,
  data = env_vars_filtered,
  f = maxnet.formula(presences, env_vars_filtered, classes = "lq")
)
pred <- predict(model_sdm, newdata = env_vars_filtered, type = "cloglog")

# Ajout dans un nouveau tableau synchronisé
sdm_ready$Prediction <- if (is.matrix(pred)) pred[,1] else pred

# 1. Nettoyage des données
sdm_ready <- sdm_data %>%
  select(Presence, Longitude, LATITUDE.x, Temperature_moy, Salinity_moy, Dissolution_moy) %>%
  drop_na()

# 2. Variables environnementales filtrées
env_vars_filtered <- sdm_ready %>%
  select(Temperature_moy, Salinity_moy, Dissolution_moy) %>%
  select(where(~ sd(.x) > 1e-6))  # garde uniquement les variables qui varient

# 3. Création du modèle SANS formule automatique
model_sdm <- maxnet(
  p = sdm_ready$Presence,
  data = env_vars_filtered,
  f = ~ Temperature_moy + Salinity_moy + Dissolution_moy  # à adapter si tu en retires
)

# 4. Prédiction robuste
pred <- predict(model_sdm, newdata = env_vars_filtered, type = "cloglog")

# 5. Ajouter dans les données (vérifie que les dimensions collent)
sdm_ready$Prediction <- as.vector(pred)


sdm_ready <- sdm_data %>%
  select(Presence, Longitude, LATITUDE.x, Temperature_moy, Salinity_moy, Dissolution_moy) %>%
  drop_na()

# Vérifie la variance
env_vars_filtered <- sdm_ready %>%
  select(Temperature_moy, Salinity_moy, Dissolution_moy) %>%
  select(where(~ sd(.x) > 1e-6))
# Modèle logistique : présence (1 = sud) vs environnement
model_glm <- glm(Presence ~ ., data = cbind(Presence = sdm_ready$Presence, env_vars_filtered), family = binomial)
summary(model_glm)
sdm_ready$Prediction <- predict(model_glm, newdata = env_vars_filtered, type = "response")
ggplot(sdm_ready, aes(x = Longitude, y = LATITUDE.x, color = Prediction)) +
  geom_point(size = 2) +
  scale_color_viridis_c() +
  theme_minimal() +
  labs(title = "SDM (GLM) - Groupe Sud (Clusters 1 & 2)", color = "Probabilité")

install.packages(c("rnaturalearth", "rnaturalearthdata", "sf", "ggspatial"))

library(sf)
library(rnaturalearth)
library(rnaturalearthdata)
library(ggplot2)
library(ggspatial)

# Charger les côtes
world <- ne_countries(scale = "medium", returnclass = "sf")

# Tracer la carte avec fond
ggplot() +
  geom_sf(data = world, fill = "gray95", color = "gray70") +
  geom_point(data = sdm_ready, aes(x = Longitude, y = LATITUDE.x, color = Prediction), size = 2) +
  scale_color_viridis_c(name = "Probabilité\nGroupe Sud", option = "C") +
  coord_sf(xlim = c(-70, -45), ylim = c(38, 60), expand = FALSE) +
  annotation_scale(location = "bl", width_hint = 0.5) +
  annotation_north_arrow(location = "tl", which_north = "true", 
                         style = north_arrow_fancy_orienteering) +
  theme_minimal() +
  labs(title = "SDM - Groupe Sud (Clusters 1 & 2)",
       subtitle = "Probabilité selon les conditions environnementales")

# Créer un indicateur binaire : Nord = 1 si Cluster 3 ou 4, sinon 0
sdm_nord <- full_data %>%
  mutate(Presence = ifelse(Cluster %in% c(3, 4), 1, 0)) %>%
  select(Presence, Longitude, LATITUDE.x, Temperature_moy, Salinity_moy, Dissolution_moy) %>%
  drop_na()
# Garde uniquement les variables qui varient suffisamment
env_nord <- sdm_nord %>%
  select(Temperature_moy, Salinity_moy, Dissolution_moy) %>%
  select(where(~ sd(.x) > 1e-6))
# Modèle logistique : probabilité d’appartenance au groupe Nord
model_nord <- glm(Presence ~ ., data = cbind(Presence = sdm_nord$Presence, env_nord), family = binomial)

# Prédiction
sdm_nord$Prediction <- predict(model_nord, newdata = env_nord, type = "response")

# Charger la carte du monde (si pas encore fait)
library(sf)
library(rnaturalearth)
library(rnaturalearthdata)
library(ggspatial)

world <- ne_countries(scale = "medium", returnclass = "sf")

# Affichage
ggplot() +
  geom_sf(data = world, fill = "gray95", color = "gray70") +
  geom_point(data = sdm_nord, aes(x = Longitude, y = LATITUDE.x, color = Prediction), size = 2) +
  scale_color_viridis_c(name = "Probabilité\nGroupe Nord", option = "C") +
  coord_sf(xlim = c(-70, -45), ylim = c(38, 60), expand = FALSE) +
  annotation_scale(location = "bl", width_hint = 0.5) +
  annotation_north_arrow(location = "tl", which_north = "true",
                         style = north_arrow_fancy_orienteering) +
  theme_minimal() +
  labs(title = "SDM - Groupe Nord (Clusters 3 & 4)",
       subtitle = "Probabilité selon les conditions environnementales")




