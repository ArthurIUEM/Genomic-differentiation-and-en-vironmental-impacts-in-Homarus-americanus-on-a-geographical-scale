# Charger les packages
```
library(ggplot2)
library(dplyr)
```
# Charger les données
```
acp <- read.table("ACP_Lobster.eigenvec", header=FALSE)
depth <- read.table("Lobster1MB.idepth", header=TRUE)
eigenval <- scan("ACP_Lobster.eigenval")
```
# Calcul de la variance expliquée
```
var_explained <- eigenval / sum(eigenval)
var_explained_percent <- round(var_explained * 100, 1)
```
# Renommer les colonnes de l'ACP
```
colnames(acp) <- c("FID", "IID", paste0("PC", 1:(ncol(acp)-2)))
```
# Vérifier et renommer la colonne FID dans depth si besoin
```
if (!"FID" %in% colnames(depth)) {
  colnames(depth)[1] <- "FID"
}
```
# Fusionner ACP avec profondeur
```
acp <- acp %>% select(-IID) %>% merge(depth, by="FID")
```
# Vérifier la colonne MEAN_DEPTH
```
if (!"MEAN_DEPTH" %in% colnames(acp)) {
  stop("Erreur : La colonne MEAN_DEPTH est absente. Vérifiez le fichier Lobster1MB.idepth.")
}
```
# Filtrer les 5% supérieurs en PC1
```
upper_pc1 <- quantile(acp$PC1, 0.95)
acp <- acp %>% filter(PC1 <= upper_pc1)
```
# Graphique PC1 vs PC2
```
p <- ggplot(acp, aes(x=PC1, y=PC2, color=MEAN_DEPTH)) +
  geom_point(size=2) +
  scale_color_distiller(palette="Spectral", direction=-1) +
  theme_minimal(base_size = 14) +
  labs(
    title = "PC1 vs PC2",
    x = paste0("PC1 (", var_explained_percent[1], "%)"),
    y = paste0("PC2 (", var_explained_percent[2], "%)"),
    color = "Sequencing depth"
  ) +
  theme(
    axis.title = element_text(size=16),
    axis.text = element_text(size=13),
    legend.title = element_text(size=14),
    legend.text = element_text(size=12),
    plot.title = element_text(size=18, face="bold")
  )
```
# Afficher le graphique 
```
print(p)
```
