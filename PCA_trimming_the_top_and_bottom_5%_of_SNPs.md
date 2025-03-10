ACP en retirant le top 5% et le bottom 5% des SNPs

library(ggplot2) library(dplyr) library(gridExtra)

Charger les données
acp <- read.table("ACP_Lobster.eigenvec", header=FALSE) depth <- read.table("Lobster1MB.idepth", header=TRUE)

Renommer les colonnes de l'ACP
colnames(acp) <- c("FID", "IID", paste0("PC", 1:(ncol(acp)-2)))

Vérifier et renommer la colonne FID dans depth si nécessaire
if (!"FID" %in% colnames(depth)) { colnames(depth)[1] <- "FID" }

Supprimer la colonne IID et fusionner avec la profondeur de séquençage
acp <- acp %>% select(-IID) %>% merge(depth, by="FID")

Vérifier la présence de la colonne MEAN_DEPTH
if (!"MEAN_DEPTH" %in% colnames(acp)) { stop("Erreur : La colonne MEAN_DEPTH est absente. Vérifiez le fichier Lobster1MB.idepth.") }

Appliquer un trimming des 5% extrêmes pour chaque PC utilisé
for (i in 1:9) { lower_pc <- quantile(acp[[paste0("PC", i)]], 0.05) upper_pc <- quantile(acp[[paste0("PC", i)]], 0.95) acp <- acp %>% filter(acp[[paste0("PC", i)]] >= lower_pc & acp[[paste0("PC", i)]] <= upper_pc) }

Créer une liste de graphiques pour PC1 vs PC2, PC2 vs PC3, ..., PC9 vs PC10
plot_list <- list() for (i in 1:9) { p <- ggplot(acp, aes_string(x=paste0("PC", i), y=paste0("PC", i+1), color="MEAN_DEPTH")) + geom_point(size=2) + scale_color_viridis_c() + theme_minimal() + labs(title=paste0("PC", i, " vs PC", i+1), x=paste0("PC", i), y=paste0("PC", i+1), color="Profondeur")

plot_list[[i]] <- p }

Afficher tous les graphes dans une grille
do.call(grid.arrange, c(plot_list, ncol=3)) write.table(acp, file="Filtered_ACP_Lobster.txt", quote=FALSE, row.names=FALSE, sep="\t")
