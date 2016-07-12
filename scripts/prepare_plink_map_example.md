---
title: "Prepare Plink map file"
author: "Tim Knutsen"
date: "11 7 2016"
output: html_document
---



* From http://bioinformatics.tecnoparco.org/SNPchimp/index.php/download/download-cow-data choose the correct Chip-version
* Download the file and rename to convention agreed on. 

### Get SNP-order from raw data.
Grep and cut all markers from the first animal in the GenomeList Illumina file.

**Example**

```bash
grep '11190319_1210' FinalReport_54kV2_feb2011_ed1.txt | cut -f 1 > illumina54k_v2_markerlist.txt
```

### Reorder annotation file to match raw-data marker order.

```r
library(readr)
suppressPackageStartupMessages(library(dplyr))
suppressPackageStartupMessages(library(tidyr))
illumina54k_v2_markerlist <-
read_csv(
"~/for_folk/geno/geno_imputation/genotype_rawdata/illumina_54k_v2/illumina54k_v2_markerlist.txt",
col_names = "SNP_rawdata"
)

illumina54k_v2_annotationfile <-
  read_delim(
  "~/for_folk/geno/geno_imputation/genotype_rawdata/marker_mapfiles/illumina54k_v2_annotationfile.txt",
  "\t",
  escape_double = FALSE,
  col_types = cols(chromosome = col_character())
  )
  
illumina54k_v2_markerlist.map <-
  inner_join(
  illumina54k_v2_markerlist,
  illumina54k_v2_annotationfile,
  by = c("SNP_rawdata" = "SNP_name")
  ) %>% 
  mutate(cM = 0) %>% 
  select(chromosome, SNP_rawdata, cM, position)

# Check that order is preseved
identical(illumina54k_v2_markerlist$SNP_rawdata, illumina54k_v2_markerlist.map$SNP_rawdata)
```

```
## [1] TRUE
```

```r
# Write file
write_tsv(
  illumina54k_v2_markerlist.map,
  "~/for_folk/geno/geno_imputation/genotype_rawdata/plink_format/FinalReport_54kV2_feb2011_ed1.map",
  col_names = FALSE
  )
```

