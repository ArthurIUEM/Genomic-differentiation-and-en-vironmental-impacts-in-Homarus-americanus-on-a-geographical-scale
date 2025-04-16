
# selection des outliers pour les differentes PC
pc1 <- read.table("PC1_assoc.PC1.glm.linear", header = TRUE)
colnames(pc1) <- c("CHROM", "POS", "ID", "REF", "ALT", "PROVISIONAL_REF", "A1", "OMITTEDA1_FREQ", 
                     "TEST", "OBS_CT", "BETA", "SE", "T_STAT", "P", "ERRCODE")
outliers_pc1 <- pc1[pc1$P < 1e-5, "ID"]
write.table(outliers_pc1, "snps_PC1.txt", row.names = FALSE, col.names = FALSE, quote = FALSE)
pc2 <- read.table("PC2_assoc.PC2.glm.linear", header = TRUE)
colnames(pc2) <- c("CHROM", "POS", "ID", "REF", "ALT", "PROVISIONAL_REF", "A1", "OMITTEDA1_FREQ", 
                     "TEST", "OBS_CT", "BETA", "SE", "T_STAT", "P", "ERRCODE")
outliers_pc2 <- pc2[pc2$P < 1e-5, "ID"]
write.table(outliers_pc2, "snps_PC2.txt", row.names = FALSE, col.names = FALSE, quote = FALSE)
pc3 <- read.table("PC3_assoc.PC3.glm.linear", header = TRUE)
colnames(pc3) <- c("CHROM", "POS", "ID", "REF", "ALT", "PROVISIONAL_REF", "A1", "OMITTEDA1_FREQ", 
                     "TEST", "OBS_CT", "BETA", "SE", "T_STAT", "P", "ERRCODE")
outliers_pc3 <- pc3[pc3$P < 1e-5, "ID"]
write.table(outliers_pc3, "snps_PC3.txt", row.names = FALSE, col.names = FALSE, quote = FALSE)
pc4 <- read.table("PC4_assoc.PC4.glm.linear", header = TRUE)
colnames(pc4) <- c("CHROM", "POS", "ID", "REF", "ALT", "PROVISIONAL_REF", "A1", "OMITTEDA1_FREQ", 
                     "TEST", "OBS_CT", "BETA", "SE", "T_STAT", "P", "ERRCODE")
outliers_pc4 <- pc4[pc4$P < 1e-5, "ID"]
write.table(outliers_pc4, "snps_PC4.txt", row.names = FALSE, col.names = FALSE, quote = FALSE)
