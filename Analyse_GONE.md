# Lire les fichiers
ne1 <- read.table("cluster_1_filtered.tped_GONE2_Ne", header = TRUE)
ne2 <- read.table("cluster_2_filtered.tped_GONE2_Ne", header = TRUE)
ne3 <- read.table("cluster_3_filtered.tped_GONE2_Ne", header = TRUE)
ne4 <- read.table("cluster_4_filtered.tped_GONE2_Ne", header = TRUE)

# Convertir les générations en années
ne1$Year <- 2031 - ne1$Generation * 7
ne2$Year <- 2031 - ne2$Generation * 7
ne3$Year <- 2031 - ne3$Generation * 7
ne4$Year <- 2031 - ne4$Generation * 7

# Déterminer la plage commune d'années
all_years <- sort(unique(c(ne1$Year, ne2$Year, ne3$Year, ne4$Year)))

# Ajuster les marges et la position du titre de l'axe X
par(mar = c(6, 5, 4, 2) + 0.1)  # Marge inférieure OK pour texte vertical
par(mgp = c(4.5, 1, 0))        # Titre de l'axe X légèrement abaissé mais visible

# Tracer la première courbe
plot(ne3$Year, ne3$Ne_diploids, type = "l", col = "forestgreen",
     xlab = "Year", ylab = "Effective population size (Ne)",
     main = "Historical Ne estimates across 4 clusters",
     ylim = c(0, 40000), lwd = 3, xaxt = "n",
     cex.main = 2.2, cex.lab = 2, cex.axis = 1.6)

# Ajouter les autres courbes
lines(ne2$Year, ne2$Ne_diploids, col = "red", lwd = 3)
lines(ne1$Year, ne1$Ne_diploids, col = "blue", lwd = 3)
lines(ne4$Year, ne4$Ne_diploids, col = "purple", lwd = 3)

# Ajouter les années en ticks verticaux
axis(1, at = all_years, labels = all_years, las = 2, cex.axis = 1.4)

# Ajouter la légende
legend("topleft", legend = c("Cluster 1", "Cluster 2", "Cluster 3", "Cluster 4"),
       col = c("blue", "red", "forestgreen", "purple"),
       lty = 1, lwd = 3, cex = 1.8)
