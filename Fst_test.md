# Chargement du fichier Excel
library(readxl)
library(dplyr)

# Lire la feuille de calcul
merged_data <- read_excel("Merged_Lobster_Data.xlsx")

# Sélection des colonnes et renommage
formatted_data <- merged_data %>%
  select(FID = `Sample ID 2`, Cluster = `Population ID`) %>%
  mutate(IID = FID) %>%
  select(FID, IID, Cluster)

# Enregistrement du fichier
write.table(formatted_data, "populations_fixed_modified1.clst", sep = "\t", row.names = FALSE, quote = FALSE)

# Sortir le tableau de Fst
./plink --bfile Lobster1MB --fst --within populations_fixed_modified.clst --out FST_results --allow-extra-chr

library(readxl)
library(dplyr)
library(ggplot2)

# Charger les données depuis le fichier Excel
FSTfile <- read_excel("FST_results_sorted.xlsx")

# Vérifier le nom des colonnes pour s'assurer qu'ils correspondent aux attentes
colnames(FSTfile)

# Identifier les 10 000 plus grandes valeurs de FST
Outliers <- FSTfile %>%
  slice_max(FST, n = 10000)

# Tracer les valeurs aberrantes
ggplot() +
  geom_point(data = Outliers, aes(x = POS, y = FST)) +
  facet_wrap(~CHR, scales = "free_x") +
  theme_minimal()
