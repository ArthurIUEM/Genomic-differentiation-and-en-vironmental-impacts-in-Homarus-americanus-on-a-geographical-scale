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
