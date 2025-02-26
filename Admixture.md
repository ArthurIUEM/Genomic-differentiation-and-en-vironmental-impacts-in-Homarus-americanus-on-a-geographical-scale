# Fichiers d'entrée et de sortie
INPUT_FILE="Lobster1MB.bim"
OUTPUT_FILE="subset_127968_SNPs.bim"

# Compter le nombre total de lignes dans le fichier
TOTAL_LINES=$(wc -l < "$INPUT_FILE")

# Sélectionner 127 968 lignes aléatoires avec awk
awk -v total="$TOTAL_LINES" -v num=1279679 'BEGIN {srand()} {print rand(), $0}' "$INPUT_FILE" | sort -k1,1n | cut -d ' ' -f2- | head -n 1279679 > "$OUTPUT_FILE"

# Définition des fichiers
PLINK_FILE="subset_1279679_SNPs"
OUTPUT_PREFIX="admixture_results"

# Étape 1 : Convertir le fichier .bim en fichiers PLINK (.bed, .bim, .fam)
./plink --bfile Lobster1MB --extract "$PLINK_FILE.bim" --make-bed --out "$PLINK_FILE" --allow-extra-chr

# Définition des fichiers
INPUT_FILE="subset_1279679_SNPs.bim"
OUTPUT_FILE="subset_1279679_SNPs_modified.bim"

# Remplacer les 4 premières colonnes par "0"
awk '{
    $1 = $2 = $3 = $4 = "0";
    print $0
}' OFS="\t" "$INPUT_FILE" > "$OUTPUT_FILE"

# Étape 2 : Lancer ADMIXTURE pour une valeur de K=2
admixture -j24 subset_1279679_SNPs.bed 2

# Installer ggplot2 si nécessaire
if (!requireNamespace("ggplot2", quietly = TRUE)) install.packages("ggplot2")

# Charger les bibliothèques
library(ggplot2)
library(reshape2)

# Définir les paramètres
K <- 2  # Nombre de populations ancestrales (modifier selon le fichier .Q choisi)
file_Q <- paste0("subset_1279679_SNPs.", K, ".Q")

# Charger les données
admix_data <- read.table(file_Q)

# Ajouter une colonne Individu
admix_data$Individu <- 1:nrow(admix_data)

# Convertir au format long pour ggplot
admix_long <- melt(admix_data, id.vars = "Individu")

# Créer un barplot
ggplot(admix_long, aes(x = Individu, y = value, fill = variable)) +
  geom_bar(stat = "identity") +
  scale_fill_manual(values = rainbow(K)) +  # Couleurs dynamiques
  theme_minimal() +
  labs(title = paste("Admixture Analysis (K =", K, ")"),
       x = "Individu",
       y = "Proportion d'ascendance") +
  theme(axis.text.x = element_blank(), legend.title = element_blank())


library(ggplot2)
library(tidyr)
library(dplyr)
library(readr)

# Charger le fichier .fam mis à jour (avec les noms des populations et la latitude)
fam <- read_delim("Lobster1MB_updated.fam", delim = " ", col_names = FALSE)

# Renommer les colonnes pour faciliter l'utilisation
colnames(fam) <- c("FID", "IID", "Père", "Mère", "Sexe", "Phénotype", "Latitude", "Longitude", "Nom_Pop")

# Fonction pour charger et formater les fichiers Q
load_admixture_data <- function(k) {
  Q_file <- paste0("subset_127968_SNPs.", k, ".Q")
  df <- read_delim(Q_file, delim = " ", col_names = FALSE, col_types = cols(.default = "d"))
  
  # Ajouter les colonnes FID, Nom_Pop et Latitude à chaque fichier .Q
  df <- df %>%
    mutate(FID = fam$FID, Nom_Pop = fam$Nom_Pop, Latitude = fam$Latitude, K = k) %>%
    pivot_longer(cols = starts_with("X"), names_to = "Cluster", values_to = "Proportion") %>%
    mutate(Cluster = as.numeric(gsub("X", "", Cluster)) + 1) # Ajuster les numéros de cluster
  
  return(df)
}
# Charger les données pour tous les K
K_values <- 2:10
admixture_data <- bind_rows(lapply(K_values, load_admixture_data))

# Trier les individus par latitude pour un affichage plus clair
admixture_data <- admixture_data %>%
  arrange(K, Latitude, FID)

# Créer le graphique en barres empilées
ggplot(admixture_data, aes(x = reorder(FID, Latitude), y = Proportion, fill = factor(Cluster))) +
  geom_bar(stat = "identity") +
  facet_wrap(~K, scales = "free_x") +  # Un graphique par K
  theme_minimal() +
  theme(axis.text.x = element_blank(), axis.ticks.x = element_blank()) + # Cacher les noms des individus
  labs(x = "Individus (triés par latitude)", y = "Proportion ancestrale", fill = "Cluster") +
  scale_fill_brewer(palette = "Set3")
