# Admixture

#### Définition des fichiers d'entrée et de sortie
INPUT_FILE="Lobster1MB.bim"  # Fichier source contenant les SNPs
OUTPUT_FILE="subset_127968_SNPs.bim"  # Fichier de sortie avec un sous-ensemble de SNPs

#### Compter le nombre total de lignes dans le fichier source
TOTAL_LINES=$(wc -l < "$INPUT_FILE")  # Stocke le nombre total de SNPs dans la variable TOTAL_LINES

#### Sélectionner 127,968 lignes aléatoires à l'aide d'awk
awk -v total="$TOTAL_LINES" -v num=1279679 'BEGIN {srand()} {print rand(), $0}' "$INPUT_FILE" \
  | sort -k1,1n \
  | cut -d ' ' -f2- \
  | head -n 1279679 > "$OUTPUT_FILE"
# Explication :
# - `srand()` initialise la génération de nombres aléatoires.
# - `print rand(), $0` ajoute un nombre aléatoire au début de chaque ligne.
# - `sort -k1,1n` trie les lignes selon le nombre aléatoire.
# - `cut -d ' ' -f2-` enlève la colonne aléatoire ajoutée.
# - `head -n 1279679` sélectionne les premières 127,968 lignes après tri.

#### Définition des fichiers pour PLINK
PLINK_FILE="subset_1279679_SNPs"
OUTPUT_PREFIX="admixture_results"

### Étape 1 : Conversion du fichier .bim en fichiers PLINK (.bed, .bim, .fam)
./plink --bfile Lobster1MB --extract "$PLINK_FILE.bim" --make-bed --out "$PLINK_FILE" --allow-extra-chr
# Explication :
# - `--bfile Lobster1MB` : Utilise le fichier binaire PLINK comme entrée.
# - `--extract "$PLINK_FILE.bim"` : Extrait uniquement les SNPs listés dans le fichier bim.
# - `--make-bed` : Génère les fichiers PLINK .bed, .bim et .fam.
# - `--out "$PLINK_FILE"` : Spécifie le préfixe des fichiers de sortie.
# - `--allow-extra-chr` : Permet la prise en charge de chromosomes non standards.

#### Modification du fichier .bim en remplaçant les 4 premières colonnes par "0"
INPUT_FILE="subset_1279679_SNPs.bim"
OUTPUT_FILE="subset_1279679_SNPs_modified.bim"
awk '{
    $1 = $2 = $3 = $4 = "0";  # Remplace les 4 premières colonnes par "0"
    print $0
}' OFS="\t" "$INPUT_FILE" > "$OUTPUT_FILE"

### Étape 2 : Exécuter ADMIXTURE pour une valeur de K=2
admixture -j24 subset_1279679_SNPs.bed 2
# Explication :
# - `-j24` : Utilise 24 threads pour accélérer le calcul.
# - `subset_1279679_SNPs.bed 2` : Exécute ADMIXTURE pour K=2 (deux populations ancestrales).

### Étape 3 : Analyse avec R

#### Installation de ggplot2 si nécessaire
if (!requireNamespace("ggplot2", quietly = TRUE)) install.packages("ggplot2")

#### Chargement des bibliothèques
library(ggplot2)
library(reshape2)

#### Définition des paramètres pour l'analyse
K <- 2  # Nombre de populations ancestrales à analyser
file_Q <- paste0("subset_1279679_SNPs.", K, ".Q")  # Nom du fichier contenant les proportions d'admixture

#### Chargement des données d'admixture
admix_data <- read.table(file_Q)

#### Ajout d'une colonne pour identifier les individus
admix_data$Individu <- 1:nrow(admix_data)

#### Transformation des données pour ggplot
df_long <- melt(admix_data, id.vars = "Individu")

#### Création d'un barplot représentant les proportions d'admixture
plot <- ggplot(df_long, aes(x = Individu, y = value, fill = variable)) +
  geom_bar(stat = "identity") +
  scale_fill_manual(values = rainbow(K)) +  # Utilisation d'une palette de couleurs dynamiques
  theme_minimal() +
  labs(title = paste("Admixture Analysis (K =", K, ")"),
       x = "Individu",
       y = "Proportion d'ascendance") +
  theme(axis.text.x = element_blank(), legend.title = element_blank())

### Étape 4 : Analyse pour plusieurs valeurs de K

library(tidyr)
library(dplyr)
library(readr)

#### Chargement du fichier .fam mis à jour avec les noms de population et coordonnées
fam <- read_delim("Lobster1MB_updated.fam", delim = " ", col_names = FALSE)

#### Renommage des colonnes pour plus de clarté
colnames(fam) <- c("FID", "IID", "Père", "Mère", "Sexe", "Phénotype", "Latitude", "Longitude", "Nom_Pop")

#### Fonction pour charger et transformer les fichiers Q
load_admixture_data <- function(k) {
  Q_file <- paste0("subset_127968_SNPs.", k, ".Q")
  df <- read_delim(Q_file, delim = " ", col_names = FALSE, col_types = cols(.default = "d"))
  
  df <- df %>%
    mutate(FID = fam$FID, Nom_Pop = fam$Nom_Pop, Latitude = fam$Latitude, K = k) %>%
    pivot_longer(cols = starts_with("X"), names_to = "Cluster", values_to = "Proportion") %>%
    mutate(Cluster = as.numeric(gsub("X", "", Cluster)) + 1)  # Ajuste les numéros de clusters
  
  return(df)
}

#### Chargement des données pour plusieurs valeurs de K
K_values <- 2:10  # Tester les valeurs de K de 2 à 10
admixture_data <- bind_rows(lapply(K_values, load_admixture_data))

#### Tri des individus par latitude pour améliorer la visualisation
admixture_data <- admixture_data %>%
  arrange(K, Latitude, FID)

#### Création d'un barplot empilé pour représenter l'admixture selon K
plot_all <- ggplot(admixture_data, aes(x = reorder(FID, Latitude), y = Proportion, fill = factor(Cluster))) +
  geom_bar(stat = "identity") +
  facet_wrap(~K, scales = "free_x") +  # Un graphique par valeur de K
  theme_minimal() +
  theme(axis.text.x = element_blank(), axis.ticks.x = element_blank()) +  # Masquer les noms des individus
  labs(x = "Individus (triés par latitude)", y = "Proportion ancestrale", fill = "Cluster") +
  scale_fill_brewer(palette = "Set3")
