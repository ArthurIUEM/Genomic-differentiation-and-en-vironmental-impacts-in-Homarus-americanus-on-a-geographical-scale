# Admixture

#### Input and output files
````
INPUT_FILE="Lobster1MB.bim"
OUTPUT_FILE="subset_127968_SNPs.bim"
````
#### Count the total number of lines in the file
````
TOTAL_LINES=$(wc -l < "$INPUT_FILE")
````
#### Select 127,968 random lines with awk
````
awk -v total="$TOTAL_LINES" -v num=1279679 'BEGIN {srand()} {print rand(), $0}' "$INPUT_FILE" | sort -k1,1n | cut -d ' ' -f2- | head -n 1279679 > "$OUTPUT_FILE"
````
#### File definition
````
PLINK_FILE="subset_1279679_SNPs"
OUTPUT_PREFIX="admixture_results"
````
### Step 1: Convert .bim file to PLINK files (.bed, .bim, .fam)
````
./plink --bfile Lobster1MB --extract "$PLINK_FILE.bim" --make-bed --out "$PLINK_FILE" --allow-extra-chr
````
#### File definition
````
INPUT_FILE="subset_1279679_SNPs.bim"
OUTPUT_FILE="subset_1279679_SNPs_modified.bim"
````
#### Replace the first 4 columns with “0”
````
awk '{
    $1 = $2 = $3 = $4 = "0";
    print $0
}' OFS="\t" "$INPUT_FILE" > "$OUTPUT_FILE"
````
### Step 2: Run ADMIXTURE for a K=2 value
````
admixture -j24 subset_1279679_SNPs.bed 2
````
### Step 3: Analysis on R
#### Install ggplot2 if necessary
````
if (!requireNamespace("ggplot2", quietly = TRUE)) install.packages("ggplot2")
````
#### Load libraries
````
library(ggplot2)
library(reshape2)
````
#### Set parameters
````
K <- 2  # Nombre de populations ancestrales (modifier selon le fichier .Q choisi)
file_Q <- paste0("subset_1279679_SNPs.", K, ".Q")
````
#### Load data
````
admix_data <- read.table(file_Q)
````
#### Add an Individual column
````
admix_data$Individu <- 1:nrow(admix_data)
````
#### Convert to long format for ggplot
````
admix_long <- melt(admix_data, id.vars = "Individu")
````
#### Create a barplot
````
ggplot(admix_long, aes(x = Individu, y = value, fill = variable)) +
  geom_bar(stat = "identity") +
  scale_fill_manual(values = rainbow(K)) +  # Couleurs dynamiques
  theme_minimal() +
  labs(title = paste("Admixture Analysis (K =", K, ")"),
       x = "Individu",
       y = "Proportion d'ascendance") +
  theme(axis.text.x = element_blank(), legend.title = element_blank())
````
### Step 4: Analysis of all K
````
library(ggplot2)
library(tidyr)
library(dplyr)
library(readr)
````
#### Load updated .fam file (with population names and latitude)
````
fam <- read_delim("Lobster1MB_updated.fam", delim = " ", col_names = FALSE)
````
#### Rename columns for ease of use
````
colnames(fam) <- c("FID", "IID", "Père", "Mère", "Sexe", "Phénotype", "Latitude", "Longitude", "Nom_Pop")
````
#### Function for loading and formatting Q files
````
load_admixture_data <- function(k) {
  Q_file <- paste0("subset_127968_SNPs.", k, ".Q")
  df <- read_delim(Q_file, delim = " ", col_names = FALSE, col_types = cols(.default = "d"))
  
  # Add FID, PopName and Latitude columns to each .Q file
  df <- df %>%
    mutate(FID = fam$FID, Nom_Pop = fam$Nom_Pop, Latitude = fam$Latitude, K = k) %>%
    pivot_longer(cols = starts_with("X"), names_to = "Cluster", values_to = "Proportion") %>%
    mutate(Cluster = as.numeric(gsub("X", "", Cluster)) + 1) # Ajuster les numéros de cluster
  
  return(df)
}
````
#### Load data for all K
````
K_values <- 2:10
admixture_data <- bind_rows(lapply(K_values, load_admixture_data))
````
#### Sort individuals by latitude for clearer display
````
admixture_data <- admixture_data %>%
  arrange(K, Latitude, FID)
````
#### Create stacked bar plot
````
ggplot(admixture_data, aes(x = reorder(FID, Latitude), y = Proportion, fill = factor(Cluster))) +
  geom_bar(stat = "identity") +
  facet_wrap(~K, scales = "free_x") +  # Un graphique par K
  theme_minimal() +
  theme(axis.text.x = element_blank(), axis.ticks.x = element_blank()) + # Cacher les noms des individus
  labs(x = "Individus (triés par latitude)", y = "Proportion ancestrale", fill = "Cluster") +
  scale_fill_brewer(palette = "Set3")
````
