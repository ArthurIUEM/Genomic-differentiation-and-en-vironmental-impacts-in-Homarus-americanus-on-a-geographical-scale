# Charger le fichier des assignations de clusters
clusters <- read.table("Cluster_Assignment.txt", header=TRUE, sep="\t")

# Identifier le numéro du cluster (colonne avec un '1')
Cluster_ID <- apply(clusters, 1, function(x) which(x == 1))

# Charger le fichier .fam pour récupérer les identifiants
fam <- read.table("Lobster1MB.fam", header=FALSE, sep=" ")

# Créer un dataframe avec les identifiants et le phénotype
Cluster_Pheno <- data.frame(FID=fam$V1, IID=fam$V2, Pheno=Cluster_ID)

# Sauvegarder le fichier
write.table(Cluster_Pheno, "Cluster_Pheno.txt", col.names=FALSE, row.names=FALSE, quote=FALSE, sep="\t")
