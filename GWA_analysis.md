
# Charger le fichier des assignations de clusters
clusters <- read.table("Cluster_assignments.txt", header=TRUE, sep="\t")

# Identifier le numéro du cluster (colonne avec un '1')
Cluster_ID <- apply(clusters, 1, function(x) which(x == 1))

# Charger le fichier .fam pour récupérer les identifiants
fam <- read.table("data.fam", header=FALSE, sep=" ")

# Créer un dataframe avec les identifiants et le phénotype
Cluster_Pheno <- data.frame(FID=fam$V1, IID=fam$V2, Pheno=Cluster_ID)

# Sauvegarder le fichier
write.table(Cluster_Pheno, "Cluster_Pheno.txt", col.names=FALSE, row.names=FALSE, quote=FALSE, sep="\t")


./plink --bfile Lobster1MB --pheno Cluster_Pheno.txt --assoc --allow-extra-chr --allow-no-sex --out output_GWA

# Charger les bibliothèques nécessaires
library(qqman)

# Calculer le nombre de SNPs à sélectionner pour les 0,1% les plus significatifs
n_significant <- ceiling(nrow(data) * 0.001)

# Sélectionner les 0,1% des SNPs les plus significatifs (P-value la plus faible)
top_snps <- data[order(data$P), ][1:n_significant, ]

# Sélectionner un échantillon aléatoire de 20 000 SNPs
set.seed(123)  # Pour rendre l'échantillonnage reproductible
random_snps <- data[sample(nrow(data), 20000), ]

# Combiner les SNPs les plus significatifs et l'échantillon aléatoire
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
