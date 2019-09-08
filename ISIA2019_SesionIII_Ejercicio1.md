## Mendelian Randomization 

	- **Author(s):** Sara Bandres-Ciga
	- **Date Last Updated:** Sept 2019

### Cargamos las librerias
```
library(data.table)
library(TwoSampleMR)
library(MRInstruments)
library(devtools)
```

## Leemos las variables instrumentales del GWAS de Acido Urico
```
URI <- read.table("Acido_Urico_instrumento.txt", header =TRUE)
URI$effect_allele <- as.character(URI$effect_allele)
URI$other_allele <- as.character(URI$other_allele)
URI$beta <- as.numeric(URI$beta)
URI$SNP <- as.character(URI$SNP)
Exp_data <- format_data(URI, type="exposure")
Exp_data <- clump_data(Exp_data)
```

## Leemos las variables instrumentales del GWAS de Enfermedad de Parkinson
En este casos son los SNPs de Acido Urico que hemos extraido de las Summary Stats de Enfermedad de Parkinson
```	
PD <- read.table("PD_Acido_Urico.txt", header =TRUE)
PD$effect_allele <- as.character(PD$effect_allele)
PD$other_allele <- as.character(PD$other_allele)
PD$SNP <- as.character(PD$SNP)
Out_data <- format_data(PD, type="outcome")
```
### Harmonizamos las Summary statistics del GWAS de acido urico y los summary statistics referentes al GWAS de Parkinson
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
