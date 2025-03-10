#!/bin/bash

# Définition des noms de fichiers
BED_FILE="Lobster1MB.bed"
BIM_FILE="Lobster1MB.bim"
FAM_FILE="Lobster1MB.fam"
PREFIX="Lobster1MB"

# Vérifier si PLINK est installé
if ! command -v plink &> /dev/null; then
    echo "PLINK n'est pas installé. Veuillez l'installer pour exécuter ce script."
    exit 1
fi

# Vérifier que les fichiers nécessaires existent
if [[ ! -f $BED_FILE || ! -f $BIM_FILE || ! -f $FAM_FILE ]]; then
    echo "Un ou plusieurs fichiers nécessaires ($BED_FILE, $BIM_FILE, $FAM_FILE) sont manquants."
    exit 1
fi

# Analyse d'hétérozygotie et consanguinité
echo "Calcul en cours de l'hétérozygotie ..."
./plink --bfile $PREFIX --het --out heterozygosity_results --allow-extra-chr

# Analyse de l'homozygotie (Runs of Homozygosity - ROH)
echo "Calcul des segments homozygotes (ROH)..."
./plink --bfile $PREFIX --homozyg --out roh_results --allow-extra-chr

# Analyse de parenté (IBS/IBD)
echo "Calcul de la parenté basée sur IBS/IBD..."
./plink --bfile $PREFIX --genome --out relatedness_results --allow-extra-chr

echo "Toutes les analyses sont terminées."
