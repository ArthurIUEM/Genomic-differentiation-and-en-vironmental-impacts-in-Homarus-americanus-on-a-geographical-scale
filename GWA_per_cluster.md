GWA per cluster

awk 'NR==FNR {a[$1]=$3; next} {print $1, $2, a[$2]}' Cluster_Assigned1.txt Lobster1MB.fam > phenotype1.txt

./plink --bfile Lobster1MB \
      --pheno phenotype1.txt \
      --assoc \
      --allow-extra-chr \
      --allow-no-sex \
      --out GWA_Cluster1

awk 'NR==FNR {a[$1]=$3; next} {print $1, $2, a[$2]}' Cluster_Assigned2.txt Lobster1MB.fam > phenotype2.txt

./plink --bfile Lobster1MB \
      --pheno phenotype2.txt \
      --assoc \
      --allow-extra-chr \
      --allow-no-sex \
      --out GWA_Cluster2

awk 'NR==FNR {a[$1]=$3; next} {print $1, $2, a[$2]}' Cluster_Assigned3.txt Lobster1MB.fam > phenotype3.txt

./plink --bfile Lobster1MB \
      --pheno phenotype3.txt \
      --assoc \
      --allow-extra-chr \
      --allow-no-sex \
      --out GWA_Cluster3

awk 'NR==FNR {a[$1]=$3; next} {print $1, $2, a[$2]}' Cluster_Assigned4.txt Lobster1MB.fam > phenotype4.txt

./plink --bfile Lobster1MB \
      --pheno phenotype4.txt \
      --assoc \
      --allow-extra-chr \
      --allow-no-sex \
      --out GWA_Cluster4
