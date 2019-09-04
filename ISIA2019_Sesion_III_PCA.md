# Estudio de la sub-estructura de poblaciones mediante analisis de componentes principales (PCA)
	- **Author(s):** Ignacio Fernandez Mata, Sara Bandres-Ciga
	- **Date Last Updated:** Sept. 2019

## Para comenzar, instalamos R studio 
```
https://www.rstudio.com/products/rstudio/download/
```

## Abrimos R studio y nos situamos en el directorio 
```
setwd("~/Users/bandrescigas/Desktop/")
```

## Instalamos y cargamos las librerias
```
install.packages("data.table") 
install.packages("gridExtra") 
install.packages("ggplot2") 
library("data.table")
library("gridExtra")
library("ggplot2")
```

## Leemos el archivo de componentes principales (PCs)
```
pcs <- fread("plot_all_data_edit_with_hapmap.txt", header = T)
```

## Leemos el archivo generado por FastStructure y le otorgamos un encabezado
```
struct <- fread("fastStructureEstimates_K4.tab", header = F)
names(struct) <- c("FID","IID","pcntEurope","pcntOther","pcntAfrica","pcntAsia")
```

## Concatenamos las columnas FID y IID para ambos archivos y los unimos
```
pcs$ID <- paste(pcs$FID, pcs$IID, sep = "_")
struct$ID <- paste(struct$FID, struct$IID, sep = "_")
data <- merge(pcs, struct, by = "ID")
```

## Recodificamos nuestras poblaciones de estudio
```
data$SOURCE[data$ORIGIN == "AFRICA_hap"] <- "AFRICA"
data$SOURCE[data$ORIGIN == "EUROPE"] <- "EUROPA"
data$SOURCE[data$ORIGIN == "ASIA_hap"] <- "ASIA"
data$SOURCE[data$ORIGIN == "Brazil"] <- "BRASIL"
data$SOURCE[data$ORIGIN == "Chile"] <- "CHILE"
data$SOURCE[data$ORIGIN == "Colombia"] <- "COLOMBIA"
data$SOURCE[data$ORIGIN == "Peru"] <- "PERU"
data$SOURCE[data$ORIGIN == "Uruguay"] <- "URUGUAY"
```

## Generamos los plots
```
plot1 <- ggplot(data, aes(PC1, PC2, shape = SOURCE, colour = pcntAsia)) + geom_point() + theme_bw() + ggtitle("Asia")
plot2 <- ggplot(data, aes(PC1, PC2, shape = SOURCE, colour = pcntAfrica)) + geom_point() + theme_bw() + ggtitle("Africa")
plot3 <- ggplot(data, aes(PC1, PC2, shape = SOURCE, colour = pcntEurope)) + geom_point() + theme_bw() + ggtitle("Europa")
plot4 <- ggplot(data, aes(PC1, PC2, shape = SOURCE, colour = pcntOther)) + geom_point() + theme_bw() + ggtitle("Other")
```

## Unimos los plots
```
plotted <- grid.arrange(plot1, plot2, plot3, plot4, nrow = 2)
```

## Exportamos los plots
```
ggsave(plotted, file = "pcntAncestriesAndPcs.png", height = 12, width = 12, units = "in")
```

## Cerramos R
```
q("no")
```
