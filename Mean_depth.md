Profondeur moyenne 

# Charger les données
depth_data <- read.table("Lobster1MB.idepth", header = TRUE)

# Vérifier les noms de colonnes
colnames(depth_data)

# Calculer la profondeur moyenne
mean_depth <- mean(depth_data$MEAN_DEPTH, na.rm = TRUE)

# Afficher le résultat
print(paste("Profondeur moyenne :", mean_depth))
