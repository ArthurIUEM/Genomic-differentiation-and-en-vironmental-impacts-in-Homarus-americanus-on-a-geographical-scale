# --- Librairies
library(data.table)
library(ggplot2)

# --- Chargement des fichiers
fst <- fread("lobster_fst.fst", header = TRUE)
bim <- fread("Lobster1MB.bim", header = FALSE)
colnames(bim) <- c("CHR", "SNP", "CM", "BP", "A1", "A2")

# --- Fusion pour les positions physiques
fst_data <- merge(fst, bim[, .(SNP, BP)], by = "SNP")

# --- Nettoyage
fst_data[, CHR := as.factor(CHR)]
fst_data[, CHR := as.numeric(as.character(CHR))]
fst_data <- fst_data[!is.na(CHR)]

# --- Garder les 10 000 SNPs les plus élevés en FST
top_fst <- fst_data[order(-FST)][1:10000]

# --- Refaire le calcul de position cumulée
top_fst <- top_fst[order(CHR, BP)]
chrom_cumlen <- top_fst[, .(maxBP = max(BP)), by = CHR]
chrom_cumlen[, offset := cumsum(data.table::shift(maxBP, fill = 0))]
top_fst <- merge(top_fst, chrom_cumlen[, .(CHR, offset)], by = "CHR")
top_fst[, pos_cum := BP + offset]

# --- Calculer les outliers : FST > 99e percentile dans chaque chromosome
top_fst[, FST_thresh := quantile(FST, 0.99), by = CHR]
top_fst[, is_outlier := FST > FST_thresh]

# --- Compter les outliers par chromosome
outlier_count <- top_fst[is_outlier == TRUE, .N, by = CHR]

# --- Barplot des chromosomes avec le plus d'outliers
ggplot(outlier_count, aes(x = as.factor(CHR), y = N)) +
  geom_col(fill = "tomato") +
  labs(
    title = "Number of FST outliers (10K) per chromosome",
    x = "Chromosome",
    y = "Number of outliers"
  ) +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))


# --- Exporter la liste des chromosomes présents dans les 50k
fwrite(axis_df[order(center), .(CHR)], "liste_chromosomes_top50k.tsv", sep = "\t")
