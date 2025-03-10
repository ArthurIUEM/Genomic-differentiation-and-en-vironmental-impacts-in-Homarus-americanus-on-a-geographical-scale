# Fst test

### Chargement des bibliothèques
library(readxl)  # Permet de lire les fichiers Excel
library(dplyr)   # Fournit des fonctions de manipulation de données

### Lecture du fichier Excel contenant les données
merged_data <- read_excel("Merged_Lobster_Data.xlsx")

### Sélection et renommage des colonnes pertinentes
formatted_data <- merged_data %>%
  select(FID = `Sample ID 2`, Cluster = `Population ID`) %>%  # Sélection des colonnes nécessaires
  mutate(IID = FID) %>%  # Création de la colonne IID en la copiant depuis FID
  select(FID, IID, Cluster)  # Réorganisation des colonnes

### Sauvegarde des données formatées dans un fichier tabulé
write.table(formatted_data, "populations_fixed_modified1.clst", sep = "\t", row.names = FALSE, quote = FALSE)

### Exécution de PLINK pour calculer les valeurs de Fst
system("./plink --bfile Lobster1MB --fst --within populations_fixed_modified.clst --out FST_results --allow-extra-chr")
# Explication :
# - `--bfile Lobster1MB` : Utilise le fichier binaire PLINK comme entrée.
# - `--fst` : Calcule les valeurs de différenciation génétique Fst.
# - `--within populations_fixed_modified.clst` : Spécifie les groupes de population.
# - `--out FST_results` : Définit le fichier de sortie contenant les résultats.
# - `--allow-extra-chr` : Autorise les chromosomes non conventionnels.

### Chargement des bibliothèques nécessaires pour l'analyse et la visualisation
library(readxl)
library(dplyr)
library(ggplot2)

### Chargement des résultats de Fst depuis un fichier Excel
FSTfile <- read_excel("FST_results_sorted.xlsx")

### Vérification des noms de colonnes pour assurer la compatibilité avec le script
colnames(FSTfile)  # Affiche les noms des colonnes du fichier chargé

### Identification des 10 000 valeurs de Fst les plus élevées
Outliers <- FSTfile %>%
  slice_max(FST, n = 10000)  # Sélectionne les 10 000 SNPs avec les Fst les plus élevés

### Création d'un graphique pour visualiser les valeurs de Fst extrêmes
plot_fst <- ggplot() +
  geom_point(data = Outliers, aes(x = POS, y = FST)) +  # Nuage de points des SNPs avec Fst élevés
  facet_wrap(~CHR, scales = "free_x") +  # Affiche un graphique par chromosome
  theme_minimal()  # Applique un thème épuré
