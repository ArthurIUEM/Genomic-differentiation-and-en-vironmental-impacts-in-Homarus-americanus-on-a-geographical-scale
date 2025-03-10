### Appliquer K-means sur les données PCA (ou UMAP) avec 4 clusters
set.seed(123)  # Assure la reproductibilité des résultats
kmeans_result <- kmeans(acp[, c("UMAP1", "UMAP2")], centers=4)  # Exécute K-means avec 4 clusters

# Ajouter les résultats du clustering au DataFrame
acp$Cluster_Kmeans <- as.factor(kmeans_result$cluster)

# ---- Création du tableau binaire (0/1) pour chaque individu et cluster ----

# Créer une matrice où chaque ligne représente un individu et chaque colonne un cluster
cluster_matrix <- as.data.frame(model.matrix(~ Cluster_Kmeans - 1, data=acp))

# Afficher un aperçu du tableau
head(cluster_matrix)

# Sauvegarder ce tableau dans un fichier pour utilisation future
write.table(cluster_matrix, file="Cluster_Membership_Table.txt", quote=FALSE, row.names=FALSE, sep="\t")

# Charger le fichier contenant les assignations des clusters
clusters <- read.table("Cluster_assignments.txt", header=TRUE, sep="\t")

# Identifier le numéro du cluster (colonne avec un '1')
Cluster_ID <- apply(clusters, 1, function(x) which(x == 1))

# Charger le fichier .fam pour récupérer les identifiants des individus
fam <- read.table("data.fam", header=FALSE, sep=" ")

# Créer un DataFrame avec les identifiants et le phénotype correspondant au cluster assigné
Cluster_Pheno <- data.frame(FID=fam$V1, IID=fam$V2, Pheno=Cluster_ID)

# Sauvegarder le fichier contenant les phénotypes basés sur les clusters
write.table(Cluster_Pheno, "Cluster_Pheno.txt", col.names=FALSE, row.names=FALSE, quote=FALSE, sep="\t")

# Exécuter une analyse d'association génétique avec PLINK
system("./plink --bfile Lobster1MB --pheno Cluster_Pheno.txt --assoc --allow-extra-chr --allow-no-sex --out output_GWA")

# Charger la bibliothèque pour la visualisation des résultats GWAS
library(qqman)

# Calculer le nombre de SNPs à sélectionner pour les 0,1% les plus significatifs
n_significant <- ceiling(nrow(data) * 0.001)

# Sélectionner les 0,1% des SNPs les plus significatifs (P-value la plus faible)
top_snps <- data[order(data$P), ][1:n_significant, ]

# Sélectionner un échantillon aléatoire de 20 000 SNPs
set.seed(123)  # Assure la reproductibilité de l'échantillonnage
random_snps <- data[sample(nrow(data), 20000), ]

# Combiner les SNPs les plus significatifs et un échantillon aléatoire
data_for_plot <- rbind(top_snps, random_snps)

# Tracer le graphique de Manhattan
manhattan(data_for_plot, 
          chr="CHR_numeric", 
          bp="BP", 
          p="P", 
          snp="SNP", 
          col=c("blue4", "orange3"), 
          suggestiveline=-log10(1e-5), 
          genomewideline=-log10(5e-8))
