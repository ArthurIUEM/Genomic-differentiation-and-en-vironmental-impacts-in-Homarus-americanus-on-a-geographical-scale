library(ggplot2)

# Charger les résultats PCA
pca_data <- read.table("ACP_Lobster.eigenvec", header=FALSE)
colnames(pca_data) <- c("ID", "Sample", paste0("PC", 1:10))

# Charger les valeurs propres pour calculer la variance expliquée
eigenval <- scan("ACP_Lobster.eigenval")
var_explained <- eigenval / sum(eigenval)
var_explained_percent <- round(var_explained * 100, 1)

# Fonctions pour générer les titres d'axes
xlab_pc <- function(i) paste0("PC", i, " (", var_explained_percent[i], "%)")
ylab_pc <- function(i) paste0("PC", i, " (", var_explained_percent[i], "%)")

# Graphique PC1 vs PC2
ggplot(pca_data, aes(x=PC1, y=PC2)) +
  geom_point(size=2, alpha=0.7) +
  theme_minimal(base_size = 15) +
  xlab(xlab_pc(1)) +
  ylab(ylab_pc(2)) +
  ggtitle("Lobster PCA: PC1 vs PC2") +
  theme(
    plot.title = element_text(size=21, face="bold"),
    axis.title = element_text(size=19),
    axis.text = element_text(size=16)
  )

