ACP par cluster

Colore par profondeur de sequencage 

# Charger les bibliothèques nécessaires
library(ggplot2)
library(dplyr)
library(gridExtra)
library(umap)
library(dbscan)  # Pour le clustering
library(ggpubr)

# Charger les données ACP et profondeur de séquençage
acp <- read.table("ACP_Lobster.eigenvec", header=FALSE)
depth <- read.table("Lobster1MB.idepth", header=TRUE)

# Renommer les colonnes de l'ACP
colnames(acp) <- c("FID", "IID", paste0("PC", 1:(ncol(acp)-2)))

# Vérifier et renommer la colonne FID dans depth si nécessaire
if (!"FID" %in% colnames(depth)) {
  colnames(depth)[1] <- "FID"
}

# Supprimer la colonne IID et fusionner avec la profondeur de séquençage
acp <- acp %>% select(-IID) %>% merge(depth, by="FID")

# Charger le fichier .fam pour récupérer la latitude et la NAFO_ZONE
fam <- read.table("Lobster1MB.fam", header=FALSE)

# Vérifier que le fichier .fam contient bien 6 colonnes
if (ncol(fam) < 6) {
  stop("Erreur : Le fichier .fam ne contient pas suffisamment de colonnes.")
}

# Renommer les colonnes du fichier .fam
colnames(fam) <- c("FID", "IID", "PID", "MID", "SEX","OK", "LATITUDE", "LONGITUDE", "NAFO_ZONE")

# Fusionner avec le tableau ACP filtré
acp <- merge(acp, fam[, c("FID", "LATITUDE")], by="FID", all.x=TRUE)

# Sélectionner uniquement les PCs pour UMAP et clustering
pc_data <- acp %>% select(starts_with("PC"))

# Appliquer UMAP
umap_result <- umap(pc_data, n_neighbors=15, min_dist=0.1, metric="euclidean")

# Ajouter les coordonnées UMAP à acp
acp$UMAP1 <- umap_result$layout[,1]
acp$UMAP2 <- umap_result$layout[,2]

# Appliquer DBSCAN pour détecter les clusters
dbscan_result <- dbscan(acp[, c("UMAP1", "UMAP2")], eps=1, minPts=10)
acp$Cluster <- as.factor(dbscan_result$cluster)

# Sauvegarder les résultats avec les clusters
write.table(acp, file="PCA_UMAP_Lobster_with_Clusters.txt", quote=FALSE, row.names=FALSE, sep="\t")

# Vérification et conversion de la colonne Depth en numérique
acp$MEAN_DEPTH <- as.numeric(as.character(acp$MEAN_DEPTH))

# Vérification des valeurs manquantes dans Depth
if (any(is.na(acp$MEAN_DEPTH))) {
  warning("Il y a des valeurs manquantes dans Depth.")
  acp <- na.omit(acp)  # Supprimer les lignes avec NA dans Depth
}

# ---- 📊 Visualisation ----

# 1️⃣ Plot UMAP coloré par MEAN_DEPTH (profondeur de séquençage) avec le dégradé bleu -> jaune
p1 <- ggplot(acp, aes(x=UMAP1, y=UMAP2, color=MEAN_DEPTH)) +
  geom_point(size=2) +
  theme_minimal() +
  labs(title="UMAP des homards coloré par profondeur de séquençage") +
  scale_color_gradient(low="blue", high="yellow")  # Dégradé bleu -> jaune

# 2️⃣ Comparaison PCA vs UMAP colorée par MEAN_DEPTH avec le même gradient
p2 <- ggplot(acp, aes(x=PC1, y=UMAP1, color=MEAN_DEPTH)) +
  geom_point(size=2) +
  theme_minimal() +
  labs(title="PC1 vs UMAP1 coloré par profondeur de séquençage") +
  scale_color_gradient(low="blue", high="yellow")

p3 <- ggplot(acp, aes(x=PC2, y=UMAP2, color=MEAN_DEPTH)) +
  geom_point(size=2) +
  theme_minimal() +
  labs(title="PC2 vs UMAP2 coloré par profondeur de séquençage") +
  scale_color_gradient(low="blue", high="yellow")

# Afficher les graphes ensemble
ggarrange(p1, p2, p3, ncol=2, nrow=2)

# ---- 📊 PCA dans chaque cluster colorée par Depth ----

cluster_pca_list <- list()
for (cl in unique(acp$Cluster)) {
  if (cl == 0) next  # Ignore le cluster "0" de DBSCAN (bruit)
  
  sub_data <- acp %>% filter(Cluster == cl) %>% select(starts_with("PC"))
  pca_within_cluster <- prcomp(sub_data, scale. = TRUE)
  
  # Convertir en DataFrame pour ggplot2
  pca_scores <- as.data.frame(pca_within_cluster$x)
  pca_scores$Cluster <- cl
  pca_scores$MEAN_DEPTH <- acp$MEAN_DEPTH[acp$Cluster == cl]  # Ajouter la profondeur de séquençage
  
  p <- ggplot(pca_scores, aes(x=PC1, y=PC2, color=MEAN_DEPTH)) +
    geom_point(size=2) +
    theme_minimal() +
    labs(title=paste("PCA pour Cluster", cl)) +
    scale_color_gradient2(low="blue", mid="red", high="yellow", midpoint=median(acp$MEAN_DEPTH))
  
  cluster_pca_list[[as.character(cl)]] <- p
}

# Afficher les PCA de chaque cluster
do.call(grid.arrange, c(cluster_pca_list, ncol=2))

# Appliquer K-means sur les données PCA (ou UMAP) avec 4 clusters
set.seed(123)  # Pour la reproductibilité
kmeans_result <- kmeans(acp[, c("UMAP1", "UMAP2")], centers=4)  # 4 clusters cette fois

# Ajouter les résultats du clustering au DataFrame
acp$Cluster_Kmeans <- as.factor(kmeans_result$cluster)

# ---- Création du tableau binaire (0/1) pour chaque individu et cluster ----

# Créer un tableau où chaque ligne représente un individu et chaque colonne un cluster
cluster_matrix <- as.data.frame(model.matrix(~ Cluster_Kmeans - 1, data=acp))

# Afficher le tableau
head(cluster_matrix)

# Sauvegarder ce tableau dans un fichier si besoin
write.table(cluster_matrix, file="Cluster_Membership_Table.txt", quote=FALSE, row.names=FALSE, sep="\t")


UMAP et PCA colore par latitude

# Charger les bibliothèques nécessaires
library(ggplot2)
library(dplyr)
library(gridExtra)
library(umap)
library(dbscan)  # Pour le clustering
library(ggpubr)

# Charger les données ACP et profondeur de séquençage
acp <- read.table("ACP_Lobster.eigenvec", header=FALSE)
depth <- read.table("Lobster1MB.idepth", header=TRUE)

# Renommer les colonnes de l'ACP
colnames(acp) <- c("FID", "IID", paste0("PC", 1:(ncol(acp)-2)))

# Vérifier et renommer la colonne FID dans depth si nécessaire
if (!"FID" %in% colnames(depth)) {
  colnames(depth)[1] <- "FID"
}

# Supprimer la colonne IID et fusionner avec la profondeur de séquençage
acp <- acp %>% select(-IID) %>% merge(depth, by="FID")

# Charger le fichier .fam pour récupérer la latitude et la NAFO_ZONE
fam <- read.table("Lobster1MB.fam", header=FALSE)

# Vérifier que le fichier .fam contient bien 6 colonnes
if (ncol(fam) < 6) {
  stop("Erreur : Le fichier .fam ne contient pas suffisamment de colonnes.")
}

# Renommer les colonnes du fichier .fam
colnames(fam) <- c("FID", "IID", "PID", "MID", "SEX","OK", "LATITUDE", "LONGITUDE", "NAFO_ZONE")

# Fusionner avec le tableau ACP filtré
acp <- merge(acp, fam[, c("FID", "LATITUDE")], by="FID", all.x=TRUE)

# Sélectionner uniquement les PCs pour UMAP et clustering
pc_data <- acp %>% select(starts_with("PC"))

# Appliquer UMAP
umap_result <- umap(pc_data, n_neighbors=15, min_dist=0.1, metric="euclidean")

# Ajouter les coordonnées UMAP à acp
acp$UMAP1 <- umap_result$layout[,1]
acp$UMAP2 <- umap_result$layout[,2]

# Appliquer DBSCAN pour détecter les clusters
dbscan_result <- dbscan(acp[, c("UMAP1", "UMAP2")], eps=1, minPts=10)
acp$Cluster <- as.factor(dbscan_result$cluster)

# Sauvegarder les résultats avec les clusters
write.table(acp, file="PCA_UMAP_Lobster_with_Clusters.txt", quote=FALSE, row.names=FALSE, sep="\t")

# Vérification et conversion de la colonne Depth en numérique
acp$MEAN_DEPTH <- as.numeric(as.character(acp$MEAN_DEPTH))

# Vérification des valeurs manquantes dans Depth
if (any(is.na(acp$MEAN_DEPTH))) {
  warning("Il y a des valeurs manquantes dans Depth.")
  acp <- na.omit(acp)  # Supprimer les lignes avec NA dans Depth
}

# ---- 📊 Visualisation ----

# 1️⃣ Plot UMAP coloré par MEAN_DEPTH (profondeur de séquençage) avec le dégradé bleu -> jaune
p1 <- ggplot(acp, aes(x=UMAP1, y=UMAP2, color=MEAN_DEPTH)) +
  geom_point(size=2) +
  theme_minimal() +
  labs(title="UMAP des homards coloré par profondeur de séquençage") +
  scale_color_gradient(low="blue", high="yellow")  # Dégradé bleu -> jaune

# 2️⃣ Comparaison PCA vs UMAP colorée par MEAN_DEPTH avec le même gradient
p2 <- ggplot(acp, aes(x=PC1, y=UMAP1, color=MEAN_DEPTH)) +
  geom_point(size=2) +
  theme_minimal() +
  labs(title="PC1 vs UMAP1 coloré par profondeur de séquençage") +
  scale_color_gradient(low="blue", high="yellow")

p3 <- ggplot(acp, aes(x=PC2, y=UMAP2, color=MEAN_DEPTH)) +
  geom_point(size=2) +
  theme_minimal() +
  labs(title="PC2 vs UMAP2 coloré par profondeur de séquençage") +
  scale_color_gradient(low="blue", high="yellow")

# Afficher les graphes ensemble
ggarrange(p1, p2, p3, ncol=2, nrow=2)

# ---- 📊 PCA dans chaque cluster colorée par Depth ----

cluster_pca_list <- list()
for (cl in unique(acp$Cluster)) {
  if (cl == 0) next  # Ignore le cluster "0" de DBSCAN (bruit)
  
  sub_data <- acp %>% filter(Cluster == cl) %>% select(starts_with("PC"))
  pca_within_cluster <- prcomp(sub_data, scale. = TRUE)
  
  # Convertir en DataFrame pour ggplot2
  pca_scores <- as.data.frame(pca_within_cluster$x)
  pca_scores$Cluster <- cl
  pca_scores$MEAN_DEPTH <- acp$MEAN_DEPTH[acp$Cluster == cl]  # Ajouter la profondeur de séquençage
  
  p <- ggplot(pca_scores, aes(x=PC1, y=PC2, color=MEAN_DEPTH)) +
    geom_point(size=2) +
    theme_minimal() +
    labs(title=paste("PCA pour Cluster", cl)) +
    scale_color_gradient2(low="blue", mid="red", high="yellow", midpoint=median(acp$MEAN_DEPTH))
  
  cluster_pca_list[[as.character(cl)]] <- p
}

# Afficher les PCA de chaque cluster
do.call(grid.arrange, c(cluster_pca_list, ncol=2))

# Appliquer K-means sur les données PCA (ou UMAP) avec 4 clusters
set.seed(123)  # Pour la reproductibilité
kmeans_result <- kmeans(acp[, c("UMAP1", "UMAP2")], centers=4)  # 4 clusters cette fois

# Ajouter les résultats du clustering au DataFrame
acp$Cluster_Kmeans <- as.factor(kmeans_result$cluster)

# ---- Création du tableau binaire (0/1) pour chaque individu et cluster ----

# Créer un tableau où chaque ligne représente un individu et chaque colonne un cluster
cluster_matrix <- as.data.frame(model.matrix(~ Cluster_Kmeans - 1, data=acp))

# Afficher le tableau
head(cluster_matrix)

# Sauvegarder ce tableau dans un fichier si besoin
write.table(cluster_matrix, file="Cluster_Membership_Table.txt", quote=FALSE, row.names=FALSE, sep="\t")
