# Variables d'entrée
plink_prefix="Lobster1MB_chrNW_024712526.1"  # Préfixe des fichiers PLINK (.bed, .bim, .fam)
output_prefix="ACP_chr_NW_024712526.1"       # Préfixe pour les fichiers de sortie

# Exécution de PCA avec PLINK 2 
./plink2 --bfile ${plink_prefix} \
       --pca biallelic-var-wts \
       --out ${output_prefix}

# Vérification de la sortie
if [ -f "${output_prefix}.eigenvec.var" ]; then
    echo "Fichier ${output_prefix}.eigenvec.var généré avec succès."
else
    echo "Erreur : le fichier .eigenvec.var n'a pas été généré."
fi

# Variables d'entrée       
plink_prefix="Lobster1MB_chrNW_024713129.1"  
output_prefix="ACP_chr_NW_024713129.1"   

# Exécution de PCA avec PLINK 2 
./plink2 --bfile ${plink_prefix} \
       --pca biallelic-var-wts \
       --out ${output_prefix}

# Vérification de la sortie
if [ -f "${output_prefix}.eigenvec.var" ]; then
    echo "Fichier ${output_prefix}.eigenvec.var généré avec succès."
else
    echo "Erreur : le fichier .eigenvec.var n'a pas été généré."
fi

# Variables d'entrée       
plink_prefix="Lobster1MB_chrNW_024713650.1"  
output_prefix="ACP_chr_NW_024713650.1" 

# Exécution de PCA avec PLINK 2 
./plink2 --bfile ${plink_prefix} \
       --pca biallelic-var-wts \
       --out ${output_prefix}

# Vérification de la sortie
if [ -f "${output_prefix}.eigenvec.var" ]; then
    echo "Fichier ${output_prefix}.eigenvec.var généré avec succès."
else
    echo "Erreur : le fichier .eigenvec.var n'a pas été généré."
fi

# Variables d'entrée       
plink_prefix="Lobster1MB_chrNW_024719665.1"  
output_prefix="ACP_chr_NW_024719665.1" 

# Exécution de PCA avec PLINK 2
./plink2 --bfile ${plink_prefix} \
       --pca biallelic-var-wts \
       --out ${output_prefix}

# Vérification de la sortie
if [ -f "${output_prefix}.eigenvec.var" ]; then
    echo "Fichier ${output_prefix}.eigenvec.var généré avec succès."
else
    echo "Erreur : le fichier .eigenvec.var n'a pas été généré."
fi

# Variables d'entrée
plink_prefix="Lobster1MB_chrNW_024737097.1"  
output_prefix="ACP_chr_NW_024737097.1" 

# Exécution de PCA avec PLINK 2 
./plink2 --bfile ${plink_prefix} \
       --pca biallelic-var-wts \
       --out ${output_prefix}

# Vérification de la sortie
if [ -f "${output_prefix}.eigenvec.var" ]; then
    echo "Fichier ${output_prefix}.eigenvec.var généré avec succès."
else
    echo "Erreur : le fichier .eigenvec.var n'a pas été généré."
fi

# Variables d'entrée
plink_prefix="Lobster1MB_chrNW_024737932.1"  
output_prefix="ACP_chr_NW_024737932.1" 

# Exécution de PCA avec PLINK 2 
./plink2 --bfile ${plink_prefix} \
       --pca biallelic-var-wts \
       --out ${output_prefix}

# Vérification de la sortie
if [ -f "${output_prefix}.eigenvec.var" ]; then
    echo "Fichier ${output_prefix}.eigenvec.var généré avec succès."
else
    echo "Erreur : le fichier .eigenvec.var n'a pas été généré."
fi

# Variables d'entrée
plink_prefix="Lobster1MB_chrNW_024743709.1"  
output_prefix="ACP_chr_NW_024743709.1" 

# Exécution de PCA avec PLINK 2 
./plink2 --bfile ${plink_prefix} \
       --pca biallelic-var-wts \
       --out ${output_prefix}

# Vérification de la sortie
if [ -f "${output_prefix}.eigenvec.var" ]; then
    echo "Fichier ${output_prefix}.eigenvec.var généré avec succès."
else
    echo "Erreur : le fichier .eigenvec.var n'a pas été généré."
fi

# Variables d'entrée
plink_prefix="Lobster1MB_chrNW_024744839.1"  
output_prefix="ACP_chr_NW_024744839.1" 

# Exécution de PCA avec PLINK 2
./plink2 --bfile ${plink_prefix} \
       --pca biallelic-var-wts \
       --out ${output_prefix}

# Vérification de la sortie
if [ -f "${output_prefix}.eigenvec.var" ]; then
    echo "Fichier ${output_prefix}.eigenvec.var généré avec succès."
else
    echo "Erreur : le fichier .eigenvec.var n'a pas été généré."
fi

# Charger ggplot2 et dplyr pour le traitement des données
library(ggplot2)
library(dplyr)

# Charger les SNP loadings issus de l'ACP
pca_loadings1 <- read.table("ACP_chr_NW_024712526.1.eigenvec.var", header=FALSE)
pca_loadings2 <- read.table("ACP_chr_NW_024713129.1.eigenvec.var", header=FALSE)
pca_loadings3 <- read.table("ACP_chr_NW_024713650.1.eigenvec.var", header=FALSE)
pca_loadings4 <- read.table("ACP_chr_NW_024719665.1.eigenvec.var", header=FALSE)
pca_loadings5 <- read.table("ACP_chr_NW_024737097.1.eigenvec.var", header=FALSE)
pca_loadings6 <- read.table("ACP_chr_NW_024737932.1.eigenvec.var", header=FALSE)
pca_loadings7 <- read.table("ACP_chr_NW_024743709.1.eigenvec.var", header=FALSE)
pca_loadings8 <- read.table("ACP_chr_NW_024744839.1.eigenvec.var", header=FALSE)

# Renommer les colonnes pour clarté
colnames(pca_loadings1) <- c("Chromosome", "SNP", "A1", "A2", "PC1_Loading", "PC2_Loading", "PC3_Loading", 
                            "PC4_Loading", "PC5_Loading", "PC6_Loading", "PC7_Loading", 
                            "PC8_Loading", "PC9_Loading", "PC10_Loading")
colnames(pca_loadings2) <- c("Chromosome", "SNP", "A1", "A2", "PC1_Loading", "PC2_Loading", "PC3_Loading", 
                            "PC4_Loading", "PC5_Loading", "PC6_Loading", "PC7_Loading", 
                            "PC8_Loading", "PC9_Loading", "PC10_Loading")
colnames(pca_loadings3) <- c("Chromosome", "SNP", "A1", "A2", "PC1_Loading", "PC2_Loading", "PC3_Loading", 
                            "PC4_Loading", "PC5_Loading", "PC6_Loading", "PC7_Loading", 
                            "PC8_Loading", "PC9_Loading", "PC10_Loading")
colnames(pca_loadings4) <- c("Chromosome", "SNP", "A1", "A2", "PC1_Loading", "PC2_Loading", "PC3_Loading", 
                            "PC4_Loading", "PC5_Loading", "PC6_Loading", "PC7_Loading", 
                            "PC8_Loading", "PC9_Loading", "PC10_Loading")
colnames(pca_loadings5) <- c("Chromosome", "SNP", "A1", "A2", "PC1_Loading", "PC2_Loading", "PC3_Loading", 
                            "PC4_Loading", "PC5_Loading", "PC6_Loading", "PC7_Loading", 
                            "PC8_Loading", "PC9_Loading", "PC10_Loading")
colnames(pca_loadings6) <- c("Chromosome", "SNP", "A1", "A2", "PC1_Loading", "PC2_Loading", "PC3_Loading", 
                            "PC4_Loading", "PC5_Loading", "PC6_Loading", "PC7_Loading", 
                            "PC8_Loading", "PC9_Loading", "PC10_Loading")
colnames(pca_loadings7) <- c("Chromosome", "SNP", "A1", "A2", "PC1_Loading", "PC2_Loading", "PC3_Loading", 
                            "PC4_Loading", "PC5_Loading", "PC6_Loading", "PC7_Loading", 
                            "PC8_Loading", "PC9_Loading", "PC10_Loading")
colnames(pca_loadings8) <- c("Chromosome", "SNP", "A1", "A2", "PC1_Loading", "PC2_Loading", "PC3_Loading", 
                            "PC4_Loading", "PC5_Loading", "PC6_Loading", "PC7_Loading", 
                            "PC8_Loading", "PC9_Loading", "PC10_Loading")

# Charger le fichier BIM
bim_data <- read.table("Lobster1MB.bim", header=FALSE)

# Renommer les colonnes du fichier BIM
colnames(bim_data) <- c("CHR", "SNP", "Genetic_Dist", "Position", "Allele1", "Allele2")

# Filtrer uniquement le chromosome d'intérêt
bim_chr1 <- bim_data %>% filter(CHR == "NW_024712526.1")
bim_chr2 <- bim_data %>% filter(CHR == "NW_024713129.1")
bim_chr3 <- bim_data %>% filter(CHR == "NW_024713650.1")
bim_chr4 <- bim_data %>% filter(CHR == "NW_024719665.1")
bim_chr5 <- bim_data %>% filter(CHR == "NW_024737097.1")
bim_chr6 <- bim_data %>% filter(CHR == "NW_024737932.1")
bim_chr7 <- bim_data %>% filter(CHR == "NW_024743709.1")
bim_chr8 <- bim_data %>% filter(CHR == "NW_024744839.1")

# Fusionner les données PCA et BIM pour récupérer les positions SNP
pca_pos1 <- merge(pca_loadings1, bim_chr, by="SNP")
pca_pos2 <- merge(pca_loadings2, bim_chr2, by="SNP")
pca_pos3 <- merge(pca_loadings3, bim_chr3, by="SNP")
pca_pos4 <- merge(pca_loadings4, bim_chr4, by="SNP")
pca_pos5 <- merge(pca_loadings5, bim_chr5, by="SNP")
pca_pos6 <- merge(pca_loadings6, bim_chr6, by="SNP")
pca_pos7 <- merge(pca_loadings7, bim_chr7, by="SNP")
pca_pos8 <- merge(pca_loadings8, bim_chr8, by="SNP")

# Tracer PC1_Loading (X) vs. Position sur le chromosome (Y)
ggplot(pca_pos, aes(x = Position, y = PC1_Loading)) +
  geom_point(alpha = 0.5, color = "blue") +
  theme_minimal() +
  labs(title = "Distribution des SNPs sur le chromosome NW_024712526.1",
       x = "Position SNP sur le chromosome",
       y = "PC1 Loading")

ggplot(pca_pos2, aes(x = PC1_Loading, y = Position)) +
  geom_point(alpha = 0.5, color = "red") +
  theme_minimal() +
  labs(title = "Distribution des SNPs sur le chromosome NW_024713129.1",
       x = "PC1 Loading",
       y = "Position SNP sur le chromosome")

ggplot(pca_pos3, aes(x = PC1_Loading, y = Position)) +
  geom_point(alpha = 0.5, color = "darkgreen") +
  theme_minimal() +
  labs(title = "Distribution des SNPs sur le chromosome NW_024713650.1",
       x = "PC1 Loading",
       y = "Position SNP sur le chromosome")

ggplot(pca_pos4, aes(x = PC1_Loading, y = Position)) +
  geom_point(alpha = 0.5, color = "orange") +
  theme_minimal() +
  labs(title = "Distribution des SNPs sur le chromosome NW_024719665.1",
       x = "PC1 Loading",
       y = "Position SNP sur le chromosome")

ggplot(pca_pos5, aes(x = PC1_Loading, y = Position)) +
  geom_point(alpha = 0.5, color = "brown") +
  theme_minimal() +
  labs(title = "Distribution des SNPs sur le chromosome NW_024737097.1",
       x = "PC1 Loading",
       y = "Position SNP sur le chromosome")

ggplot(pca_pos6, aes(x = PC1_Loading, y = Position)) +
  geom_point(alpha = 0.5, color = "black") +
  theme_minimal() +
  labs(title = "Distribution des SNPs sur le chromosome NW_024737932.1",
       x = "PC1 Loading",
       y = "Position SNP sur le chromosome")

ggplot(pca_pos7, aes(x = PC1_Loading, y = Position)) +
  geom_point(alpha = 0.5, color = "darkblue") +
  theme_minimal() +
  labs(title = "Distribution des SNPs sur le chromosome NW_024743709.1",
       x = "PC1 Loading",
       y = "Position SNP sur le chromosome")

ggplot(pca_pos8, aes(x = PC1_Loading, y = Position)) +
  geom_point(alpha = 0.5, color = "darkyellow") +
  theme_minimal() +
  labs(title = "Distribution des SNPs sur le chromosome NW_024744839.1",
       x = "PC1 Loading",
       y = "Position SNP sur le chromosome")
