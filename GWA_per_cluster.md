GWA per cluster

Cluster 1
awk 'NR==FNR {a[$1]=$3; next} {print $1, $2, a[$2]}' Cluster_Assigned1.txt Lobster1MB.fam > phenotype1.txt

./plink --bfile Lobster1MB \
      --pheno phenotype1.txt \
      --assoc \
      --allow-extra-chr \
      --allow-no-sex \
      --out GWA_Cluster1



library(qqman)
library(dplyr)

# Charger les données
gwa_data <- read.table("GWA_Cluster1.assoc", header = TRUE)

# Vérifier les colonnes et renommer si nécessaire
if (!all(c("CHR", "BP", "P") %in% colnames(gwa_data))) {
  stop("Le fichier ne contient pas les colonnes nécessaires : CHR, BP, P")
}

# Nettoyer la colonne des chromosomes
gwa_data$CHR <- gsub("^NW_0247|\\.1$", "", gwa_data$CHR)
gwa_data$CHR <- as.numeric(gwa_data$CHR)  # Convertir en numérique

# Définir le seuil pour les 0,1 % SNPs les plus significatifs
num_top_snps <- ceiling(nrow(gwa_data) * 0.001)
top_snps <- gwa_data %>% arrange(P) %>% head(num_top_snps)

# Sélectionner un échantillon aléatoire de 20 000 SNPs en arrière-plan
set.seed(42)  # Pour reproductibilité
random_snps <- gwa_data %>% sample_n(min(20000, nrow(gwa_data) - num_top_snps))

# Fusionner les données
gwa_plot_data <- bind_rows(random_snps, top_snps)

# Définir la ligne de seuil
threshold <- -log10(0.05 / nrow(gwa_data))

# Créer le Manhattan plot avec un titre
manhattan(gwa_plot_data, chr="CHR", bp="BP", p="P", col=c("blue", "orange"), 
          suggestiveline=threshold, genomewideline=FALSE, 
          chrlabs=as.character(unique(gwa_plot_data$CHR)),
          main="Manhattan Plot de l'Analyse GWA - Cluster 1")


Cluster 2
awk 'NR==FNR {a[$1]=$3; next} {print $1, $2, a[$2]}' Cluster_Assigned2.txt Lobster1MB.fam > phenotype2.txt

./plink --bfile Lobster1MB \
      --pheno phenotype2.txt \
      --assoc \
      --allow-extra-chr \
      --allow-no-sex \
      --out GWA_Cluster2

library(qqman)
library(dplyr)

# Charger les données
gwa_data <- read.table("GWA_Cluster2.assoc", header = TRUE)

# Vérifier les colonnes et renommer si nécessaire
if (!all(c("CHR", "BP", "P") %in% colnames(gwa_data))) {
  stop("Le fichier ne contient pas les colonnes nécessaires : CHR, BP, P")
}

# Nettoyer la colonne des chromosomes
gwa_data$CHR <- gsub("^NW_0247|\\.1$", "", gwa_data$CHR)
gwa_data$CHR <- as.numeric(gwa_data$CHR)  # Convertir en numérique

# Définir le seuil pour les 0,1 % SNPs les plus significatifs
num_top_snps <- ceiling(nrow(gwa_data) * 0.001)
top_snps <- gwa_data %>% arrange(P) %>% head(num_top_snps)

# Sélectionner un échantillon aléatoire de 20 000 SNPs en arrière-plan
set.seed(42)  # Pour reproductibilité
random_snps <- gwa_data %>% sample_n(min(20000, nrow(gwa_data) - num_top_snps))

# Fusionner les données
gwa_plot_data <- bind_rows(random_snps, top_snps)

# Définir la ligne de seuil
threshold <- -log10(0.05 / nrow(gwa_data))

# Créer le Manhattan plot avec un titre
manhattan(gwa_plot_data, chr="CHR", bp="BP", p="P", col=c("blue", "orange"), 
          suggestiveline=threshold, genomewideline=FALSE, 
          chrlabs=as.character(unique(gwa_plot_data$CHR)),
          main="Manhattan Plot de l'Analyse GWA - Cluster 2")


Cluster 3

awk 'NR==FNR {a[$1]=$3; next} {print $1, $2, a[$2]}' Cluster_Assigned3.txt Lobster1MB.fam > phenotype3.txt

./plink --bfile Lobster1MB \
      --pheno phenotype3.txt \
      --assoc \
      --allow-extra-chr \
      --allow-no-sex \
      --out GWA_Cluster3
library(qqman)
library(dplyr)

# Charger les données
gwa_data <- read.table("GWA_Cluster3.assoc", header = TRUE)

# Vérifier les colonnes et renommer si nécessaire
if (!all(c("CHR", "BP", "P") %in% colnames(gwa_data))) {
  stop("Le fichier ne contient pas les colonnes nécessaires : CHR, BP, P")
}

# Nettoyer la colonne des chromosomes
gwa_data$CHR <- gsub("^NW_0247|\\.1$", "", gwa_data$CHR)
gwa_data$CHR <- as.numeric(gwa_data$CHR)  # Convertir en numérique

# Définir le seuil pour les 0,1 % SNPs les plus significatifs
num_top_snps <- ceiling(nrow(gwa_data) * 0.001)
top_snps <- gwa_data %>% arrange(P) %>% head(num_top_snps)

# Sélectionner un échantillon aléatoire de 20 000 SNPs en arrière-plan
set.seed(42)  # Pour reproductibilité
random_snps <- gwa_data %>% sample_n(min(20000, nrow(gwa_data) - num_top_snps))

# Fusionner les données
gwa_plot_data <- bind_rows(random_snps, top_snps)

# Définir la ligne de seuil
threshold <- -log10(0.05 / nrow(gwa_data))

# Créer le Manhattan plot avec un titre
manhattan(gwa_plot_data, chr="CHR", bp="BP", p="P", col=c("blue", "orange"), 
          suggestiveline=threshold, genomewideline=FALSE, 
          chrlabs=as.character(unique(gwa_plot_data$CHR)),
          main="Manhattan Plot de l'Analyse GWA - Cluster 3")


Cluster 4
awk 'NR==FNR {a[$1]=$3; next} {print $1, $2, a[$2]}' Cluster_Assigned4.txt Lobster1MB.fam > phenotype4.txt

./plink --bfile Lobster1MB \
      --pheno phenotype4.txt \
      --assoc \
      --allow-extra-chr \
      --allow-no-sex \
      --out GWA_Cluster4
library(qqman)
library(dplyr)

# Charger les données
gwa_data <- read.table("GWA_Cluster4.assoc", header = TRUE)

# Vérifier les colonnes et renommer si nécessaire
if (!all(c("CHR", "BP", "P") %in% colnames(gwa_data))) {
  stop("Le fichier ne contient pas les colonnes nécessaires : CHR, BP, P")
}

# Nettoyer la colonne des chromosomes
gwa_data$CHR <- gsub("^NW_0247|\\.1$", "", gwa_data$CHR)
gwa_data$CHR <- as.numeric(gwa_data$CHR)  # Convertir en numérique

# Définir le seuil pour les 0,1 % SNPs les plus significatifs
num_top_snps <- ceiling(nrow(gwa_data) * 0.001)
top_snps <- gwa_data %>% arrange(P) %>% head(num_top_snps)

# Sélectionner un échantillon aléatoire de 20 000 SNPs en arrière-plan
set.seed(42)  # Pour reproductibilité
random_snps <- gwa_data %>% sample_n(min(20000, nrow(gwa_data) - num_top_snps))

# Fusionner les données
gwa_plot_data <- bind_rows(random_snps, top_snps)

# Définir la ligne de seuil
threshold <- -log10(0.05 / nrow(gwa_data))

# Créer le Manhattan plot avec un titre
manhattan(gwa_plot_data, chr="CHR", bp="BP", p="P", col=c("blue", "orange"), 
          suggestiveline=threshold, genomewideline=FALSE, 
          chrlabs=as.character(unique(gwa_plot_data$CHR)),
          main="Manhattan Plot de l'Analyse GWA - Cluster 4")
