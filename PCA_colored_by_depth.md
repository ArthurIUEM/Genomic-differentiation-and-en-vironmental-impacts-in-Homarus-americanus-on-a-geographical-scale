# PCA colored by depth

# Exécution de l'ACP avec PLINK
./plink --bfile Lobster1MB --pca 10 --out ACP_Lobster --allow-extra-chr

# Chargement des bibliothèques nécessaires
library(ggplot2)

# Chargement des résultats de l'ACP
pca_data <- read.table("ACP_Lobster.eigenvec", header=FALSE)
colnames(pca_data) <- c("FID", "IID", "PC1", "PC2", "PC3", "PC4", "PC5", "PC6", "PC7", "PC8", "PC9", "PC10")

# Chargement du fichier contenant les données de profondeur de séquençage
depth_data <- read.table("Lobster1MB.idepth", header=TRUE)

# Fusion des données ACP et des données de profondeur par identifiant d'échantillon
merged_data <- merge(pca_data, depth_data, by.x="IID", by.y="INDV")

# Graphique de PC1 vs PC2 coloré par la profondeur de séquençage
ggplot(merged_data, aes(x=PC1, y=PC2, color=MEAN_DEPTH)) +
  geom_point(size=3) +
  scale_color_gradientn(colors=c("blue", "cyan", "yellow", "red"), 
                        values=c(0, 0.3, 0.7, 1), 
                        limits=range(merged_data$MEAN_DEPTH)) +
  theme_minimal() +
  xlab("PC1") + 
  ylab("PC2") +
  ggtitle("ACP des homards coloré par la profondeur de séquençage") +
  labs(color="Profondeur de séquençage")
