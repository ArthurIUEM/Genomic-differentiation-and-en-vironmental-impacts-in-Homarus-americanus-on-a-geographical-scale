# Fst Test

### Loading the libraries
````
library(readxl)
library(dplyr)
````
### Read the spreadsheet
````
merged_data <- read_excel("Merged_Lobster_Data.xlsx")
````
### Column selection and renaming
````
formatted_data <- merged_data %>%
  select(FID = `Sample ID 2`, Cluster = `Population ID`) %>%
  mutate(IID = FID) %>%
  select(FID, IID, Cluster)
````
### Saving the file
````
write.table(formatted_data, "populations_fixed_modified1.clst", sep = "\t", row.names = FALSE, quote = FALSE)
````
### Output the Fst table
````
./plink --bfile Lobster1MB --fst --within populations_fixed_modified.clst --out FST_results --allow-extra-chr
````
### Loading the libraries
````
library(readxl)
library(dplyr)
library(ggplot2)
````
### Load data from Excel file
````
FSTfile <- read_excel("FST_results_sorted.xlsx")
````
### Check column names to make sure they match expectations
````
colnames(FSTfile)
````
### Identify the 10,000 largest FST values
````
Outliers <- FSTfile %>%
  slice_max(FST, n = 10000)
````

### Plotting outliers
ggplot() +
  geom_point(data = Outliers, aes(x = POS, y = FST)) +
  facet_wrap(~CHR, scales = "free_x") +
  theme_minimal()
