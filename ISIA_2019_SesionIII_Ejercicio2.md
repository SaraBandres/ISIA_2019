## Randomizacion Mendeliana

	- **Author(s):** Sara Bandres-Ciga
	- **Date Last Updated:** Sept 2019

### Cargamos las librerias
```
library(data.table)
library(TwoSampleMR)
library(MRInstruments)
library(devtools)
```

## Leemos las variables instrumentales del GWAS de LDL-Colesterol
```
COLESTEROL <- read.table("Colesterol_instrumento.txt", header =TRUE)
COLESTEROL$effect_allele <- as.character(COLESTEROL$effect_allele)
COLESTEROL$other_allele <- as.character(COLESTEROL$other_allele)
COLESTEROL$beta <- as.numeric(COLESTEROL$beta)
COLESTEROL$SNP <- as.character(COLESTEROL$SNP)
```

## Seleccionamos SNPs de LDL-colesterol independientes (Exposure)
```
Exp_data <- format_data(COLESTEROL, type="exposure")
Exp_data <- clump_data(Exp_data)
```
## Leemos las variables instrumentales del GWAS de Enfermedad de Parkinson
En este casos son los SNPs de LDL-colesterol que hemos extraido de las Summary Stats de Esclerosis lateral amiotrofica
```	
ALS <- read.table("ALS_Colesterol.txt", header =TRUE)
ALS$effect_allele <- as.character(ALS$effect_allele)
ALS$other_allele <- as.character(ALS$other_allele)
ALS$SNP <- as.character(ALS$SNP)
```

## Formateamos las variables instrumentales de esclerosis lateral amiotrofica (Outcome)
```
Out_data <- format_data(ALS, type="outcome")
```
### Harmonizamos las summary statistics del GWAS del colesterol y las summary statistics referentes al GWAS de Esclerosis lateral amiotrofica
```
dat <- harmonise_data(exposure_dat=Exp_data, outcome_dat=Out_data, action=2)
```
### Llevamos a cabo los analisis de Randomizacion Mendeliana
```
res <-mr(dat)
res
```
### Llevamos a cabo los analisis de sensibilidad
```
mr_heterogeneity(dat)
mr_pleiotropy_test(dat)
```
### Creamos nuestro Forest Plot
```
res_single <- mr_singlesnp(dat)
p2 <- mr_forest_plot(res_single)
p2[[1]]
```
