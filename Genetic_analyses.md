# Genetic analyses

Define file names
````
#!/bin/bash
BED_FILE="Lobster1MB.bed"
BIM_FILE="Lobster1MB.bim"
FAM_FILE="Lobster1MB.fam"
PREFIX="Lobster1MB"
````

Check if PLINK is installed
````
if ! command -v plink &> /dev/null; then
    echo "PLINK n'est pas installé. Veuillez l'installer pour exécuter ce script."
    exit 1
fi
````

Check that the necessary files exist
```
if [[ ! -f $BED_FILE || ! -f $BIM_FILE || ! -f $FAM_FILE ]]; then
    echo "Un ou plusieurs fichiers nécessaires ($BED_FILE, $BIM_FILE, $FAM_FILE) sont manquants."
    exit 1
fi
```

### Heterozygosity and inbreeding analysis
````
echo "Calculating heterozygosity ..."
./plink --bfile $PREFIX --het --out heterozygosity_results --allow-extra-chr

````

### Homozygosity analysis (ROH - Runs of Homozygosity)
````
echo "Calculation of homozygous segments (ROH)..."
./plink --bfile $PREFIX --homozyg --out roh_results --allow-extra-chr
````

### Parentage analysis (IBS/IBD)
````
echo "Calculation of parentage based on IBS/IBD..."
./plink --bfile $PREFIX --genome --out relatedness_results --allow-extra-chr
````
````
echo "Toutes les analyses sont terminées."
````

## Import and analysis in R

Read the .het file
````
het_data <- read.table("heterozygosity_results.het", header = TRUE)
````

Data overview
````
head(het_data)
````

Calculate F coefficients if necessary
````
het_data$F_manual <- 1 - ((het_data$N.NM. - het_data$O.HOM.) / (het_data$N.NM. - het_data$E.HOM.))
````

Show results
````
print(het_data)
````

Export results if necessary
````
write.table(het_data, file = "heterozygosity_results_with_F.txt", row.names = FALSE, quote = FALSE)
````
