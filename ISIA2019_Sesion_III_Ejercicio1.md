## Randomizacion Mendeliana

	- **Author(s):** Sara Bandres-Ciga, Ignacio Fernandez-Mata, Cornelis Blauwendraat
	- **Date Last Updated:** Sept 2019
	
## Vamos al directorio desde donde correremos los analisis
```	
cd /home/jupyter/SARA_ISIA2019/Sesion_III/randomizacion_mendeliana
```
## OPCIONAL -> Creamos un directorio donde vamos a instalar una serie de librerias
```
mkdir RLIB
```
## Abrimos el paquete de R
```
R
```
## Cargamos las librerias
```
library(data.table)
library(ggplot2)
library(desc,lib="RLIB/")
library(usethis,lib="RLIB/")
library("bitops",lib="RLIB/")
library("RCurl",lib="RLIB/")
library(devtools,lib="RLIB/")
library(TwoSampleMR,lib="RLIB/")
library(MRInstruments,lib="RLIB/")
```

## SOLO si queremos instalarlas previamente

```
install.packages("devtools", lib="RLIB/")
require(devtools,lib="RLIB/")
library(devtools,lib="RLIB/")
install.packages("desc", lib="RLIB/")
library(desc,lib="RLIB/")
library(devtools,lib="RLIB/")
install.packages("usethis", lib="RLIB/")
library(usethis,lib="RLIB/")
library(devtools,lib="RLIB/")
install_github("MRCIEU/TwoSampleMR", lib="RLIB/")
library(TwoSampleMR,lib="RLIB/")
install.packages("RCurl", lib="RLIB/")
library("RCurl",lib="RLIB/")
install.packages("bitops", lib="RLIB/")
library("bitops",lib="RLIB/")
install_github("MRCIEU/MRInstruments", lib="RLIB/")
library(MRInstruments)
```

## Leemos las variables instrumentales del GWAS de Acido Urico 
```
URI <- read.table("Acido_Urico_instrumento.txt", header =TRUE)
URI$effect_allele <- as.character(URI$effect_allele)
URI$other_allele <- as.character(URI$other_allele)
URI$beta <- as.numeric(URI$beta)
URI$SNP <- as.character(URI$SNP)
```

## Formateamos las variables instrumentales de Acido Urico (Exposure)
```
Exp_data <- format_data(URI, type="exposure")
```

## Seleccionamos SNPs de Acido Urico independientes
```
Exp_data <- clump_data(Exp_data)
```

## Leemos las variables instrumentales del GWAS de Enfermedad de Parkinson
En este casos son los SNPs de Acido Urico que hemos extraido de las Summary Stats de Enfermedad de Parkinson
```	
PD <- read.table("PD_Acido_Urico.txt", header =TRUE)
PD$effect_allele <- as.character(PD$effect_allele)
PD$other_allele <- as.character(PD$other_allele)
PD$SNP <- as.character(PD$SNP)
```

## Formateamos las variables instrumentales de Enfermedad de Parkinson (Outcome)
```	
Out_data <- format_data(PD, type="outcome")
```
## Armonizamos las summary statistics del GWAS de Acido Urico y los summary statistics referentes al GWAS de enfermedad de Parkinson
```
dat <- harmonise_data(exposure_dat=Exp_data, outcome_dat=Out_data, action=2)
```
## Llevamos a cabo los analisis de Randomizacion Mendeliana
```
res <-mr(dat)
res
fwrite(res, file = "resultados_AcidoUrico_PD_MRtests.tab", na = "NA", quote = F, row.names = F, sep = "\t")
```
## Llevamos a cabo los analisis de sensibilidad
```
mr_heterogeneity(dat)
mr_pleiotropy_test(dat)
```
## Creamos nuestro Forest plot de los resultados
```
res_single <- mr_singlesnp(dat)
p2 <- mr_forest_plot(res_single)
ggsave(p2[[1]], file="Acido_Urico_PD.jpeg", width=7, height=7)
```
## Creamos un Funnel plot para comprobar simetria
```
res_single <- mr_singlesnp(dat)
p4 <- mr_funnel_plot(res_single)
ggsave(p4[[1]], file="Funnel_plot_AcidoUrico_PD.jpeg", width=7, height=7)
```
## Llevamos a cabo el analisis de leave-one-out
```
res_loo <- mr_leaveoneout(dat)
res_loo
fwrite(res_loo, file = "resultados_AcidoUrico_PD_LOO.tab", na = "NA", quote = F, row.names = F, sep = "\t")
```

## Creamos nuestro Forest plot del analisis de leave-one-out
```
p3 <- mr_leaveoneout_plot(res_loo)
ggsave(p3[[1]], file="LOO_AcidoUrico_PD.jpeg", width=7, height=7)
```
