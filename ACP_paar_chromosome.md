# Extraire les SNPs du chromosome
awk '$1 == "024713129"' Lobster1MB.bim | cut -f2 > snp_list_024713129.txt
awk '$1 == "024719604"' Lobster1MB.bim | cut -f2 > snp_list_024719604.txt
awk '$1 == "024731852"' Lobster1MB.bim | cut -f2 > snp_list_024731852.txt
awk '$1 == "024737097"' Lobster1MB.bim | cut -f2 > snp_list_024737097.txt
awk '$1 == "024740202"' Lobster1MB.bim | cut -f2 > snp_list_024740202.txt
awk '$1 == "024741189"' Lobster1MB.bim | cut -f2 > snp_list_024741189.txt
awk '$1 == "024744839"' Lobster1MB.bim | cut -f2 > snp_list_024744839.txt

# Créer un sous-ensemble PLINK avec tous les individus
./plink --bfile Lobster1MB \
        --extract snp_list_024713129.txt \
        --make-bed \
        --out Lobster_024713129 \
        --allow-extra-chr
        
./plink --bfile Lobster1MB \
        --extract snp_list_024719604.txt \
        --make-bed \
        --out Lobster_024719604 \
        --allow-extra-chr
        
./plink --bfile Lobster1MB \
        --extract snp_list_024731852.txt \
        --make-bed \
        --out Lobster_024731852 \
        --allow-extra-chr
        
./plink --bfile Lobster1MB \
        --extract snp_list_024737097.txt \
        --make-bed \
        --out Lobster_024737097 \
        --allow-extra-chr
        
./plink --bfile Lobster1MB \
        --extract snp_list_024740202.txt \
        --make-bed \
        --out Lobster_024740202 \
        --allow-extra-chr
        
./plink --bfile Lobster1MB \
        --extract snp_list_024741189.txt \
        --make-bed \
        --out Lobster_024741189 \
        --allow-extra-chr
        
./plink --bfile Lobster1MB \
        --extract snp_list_024744839.txt \
        --make-bed \
        --out Lobster_024744839 \
        --allow-extra-chr

# Lancer l’ACP
./plink --bfile Lobster_024713129 \
        --pca \
        --out PCA_024713129 \
        --allow-extra-chr

./plink --bfile Lobster_024719604 \
        --pca \
        --out PCA_024719604 \
        --allow-extra-chr

./plink --bfile Lobster_024731852 \
        --pca \
        --out PCA_024731852 \
        --allow-extra-chr

./plink --bfile Lobster_024737097 \
        --pca \
        --out PCA_024737097 \
        --allow-extra-chr

./plink --bfile Lobster_024740202 \
        --pca \
        --out PCA_024740202 \
        --allow-extra-chr

./plink --bfile Lobster_024741189 \
        --pca \
        --out PCA_024741189 \
        --allow-extra-chr

./plink --bfile Lobster_024744839 \
        --pca \
        --out PCA_024744839 \
        --allow-extra-chr

# Graphique 
# install.packages("patchwork")  # à décommenter si besoin
library(ggplot2)
library(patchwork)

# Charger la PCA
pca <- read.table("PCA_024713129.eigenvec", header=FALSE)
colnames(pca)[1:2] <- c("FID", "IID")

# Charger les latitudes depuis le .fam
fam <- read.table("Lobster1MB.fam")
colnames(fam) <- c("FID", "IID", "PID", "MID", "Sex","OK", "Latitude")

# Fusion PCA + latitude
pca_lat <- merge(pca, fam[, c("FID", "IID", "Latitude")], by=c("FID", "IID"))

# Palette de couleur personnalisée
color_palette <- c("red", "orange", "yellow", "green", "blue")

# Graphique PC1 vs PC2
p1 <- ggplot(pca_lat, aes(x=V3, y=V4, color=Latitude)) +
  geom_point(size=2) +
  scale_color_gradientn(colors = color_palette) +
  theme_minimal() +
  labs(title="PC1 vs PC2", x="PC1", y="PC2", color="Latitude")

# Graphique PC2 vs PC3
p2 <- ggplot(pca_lat, aes(x=V4, y=V5, color=Latitude)) +
  geom_point(size=2) +
  scale_color_gradientn(colors = color_palette) +
  theme_minimal() +
  labs(title="PC2 vs PC3", x="PC2", y="PC3", color="Latitude")

# Graphique PC3 vs PC4
p3 <- ggplot(pca_lat, aes(x=V5, y=V6, color=Latitude)) +
  geom_point(size=2) +
  scale_color_gradientn(colors = color_palette) +
  theme_minimal() +
  labs(title="PC3 vs PC4", x="PC3", y="PC4", color="Latitude")

# Combiner les graphiques avec un titre général
final_plot <- (p1 | p2 | p3) + 
  plot_annotation(title = "Analyse en composantes principales - Chromosome 024713129")

final_plot
