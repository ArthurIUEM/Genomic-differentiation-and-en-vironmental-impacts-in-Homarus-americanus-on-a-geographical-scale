## Charger les librairies
library(data.table)
library(ggplot2)

## Charger les fichiers
fst <- fread("lobster_fst.fst", header=TRUE)
bim <- fread("Lobster_no_024712526.bim", header=FALSE)
colnames(bim) <- c("CHR", "SNP", "CM", "BP", "A1", "A2")

## Merge pour récupérer les positions
fst_data <- merge(fst, bim[, .(SNP, BP)], by = "SNP")

## Nettoyage
fst_data[, CHR := as.factor(CHR)]
fst_data[, CHR := as.numeric(as.character(CHR))]
fst_data <- fst_data[!is.na(CHR)]

## Ordre et position cumulée
fst_data <- fst_data[order(CHR, BP)]
chrom_cumlen <- fst_data[, .(maxBP = max(BP)), by = CHR]
chrom_cumlen[, offset := cumsum(data.table::shift(maxBP, fill = 0))]
fst_data <- merge(fst_data, chrom_cumlen[, .(CHR, offset)], by = "CHR")
fst_data[, pos_cum := BP + offset]

## Position des étiquettes pour le plot général (optionnel)
axis_df <- fst_data[, .(center = mean(pos_cum)), by = CHR]

## Zoom sur le chromosome 24713129
fst_zoom <- fst_data[CHR == 24713129]

## Filtrer les SNPs avec FST > 0.05
fst_high <- fst_zoom[FST > 0.05]

## Sauvegarder le tableau filtré
fwrite(fst_high, "SNPs_FST_sup_0.05_chr24713129.tsv", sep = "\t")

## Plot zoomé
ggplot(fst_zoom, aes(x = BP, y = FST)) +
  geom_point(size = 0.6, color = "grey60") +
  geom_point(data = fst_high, aes(x = BP, y = FST), color = "red", size = 0.8) +
  labs(x = "Position (bp)", y = "FST", title = "Zoom sur le chromosome 24713129") +
  theme_bw() +
  theme(
    panel.grid.major.x = element_blank(),
    panel.grid.minor.x = element_blank()
  )
