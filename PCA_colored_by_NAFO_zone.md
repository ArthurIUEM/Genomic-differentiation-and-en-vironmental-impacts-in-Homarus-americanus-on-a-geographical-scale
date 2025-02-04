# Remplacer la colonne des identifiants SNPs par le numero de leur ligne pour qu'ils aient un identifiant unique
awk '{ $2 = NR; print }' Lobster1MB.bim > Lobster1MB2.bim

# Filtrer le fichier .bim en Bash
awk '$1 == "NW_024712526.1"' Lobster1MB2.bim > Lobster1MB_NW_024712526.1.bim


