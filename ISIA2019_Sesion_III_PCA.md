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
names(struct) <- c("FID","IID","pcntEurope","pcntLatino","pcntAfrica","pcntAsia")
```

## Concatenamos las columnas FID y IID para ambos archivos y los unimos
```
pcs$ID <- paste(pcs$FID, pcs$IID, sep = "_")
struct$ID <- paste(struct$FID, struct$IID, sep = "_")
data <- merge(pcs, struct, by = "ID")
```
## Seleccionamos las poblaciones HapMap3 que queremos comparar. 
En este caso la poblacion Europea, la Africana y la Asiatica versus la poblacion de Peru
```
data2 <- subset(data, ORIGIN == "AFRICA_hap" | ORIGIN == "EUROPE" | ORIGIN == "ASIA_hap" | ORIGIN == "Peru")
```

## Recodificamos nuestras poblaciones de estudio en funcion del origen
```
data2$SOURCE[data2$ORIGIN == "AFRICA_hap"] <- "AFRICA"
data2$SOURCE[data2$ORIGIN == "EUROPE"] <- "EUROPA"
data2$SOURCE[data2$ORIGIN == "ASIA_hap"] <- "ASIA"
data2$SOURCE[data2$ORIGIN == "Peru"] <- "PERU"
```

## Generamos el plot de las poblaciones HapMap3 (Europea, Africana y Asiatica) con respecto a la poblacion Peruana
```
plot1 <- ggplot(data2, aes(PC1, PC2, shape = SOURCE, colour = pcntLatino)) + geom_point() + theme_bw() + ggtitle("Peru")

```
## Ademas, podemos comparar las poblaciones HapMap3 (Europea, Africana y Asiatica) con respecto a toda la poblacion Latina
En este caso la poblacion Europea, la Africana y la Asiatica versus las poblaciones de Peru, Uruguay, Colombia, Chile y Brasil.
```
data3 <- subset(data, ORIGIN == "AFRICA_hap" | ORIGIN == "EUROPE" | ORIGIN == "ASIA_hap" | ORIGIN == "Peru" | ORIGIN == "Chile" | ORIGIN == "Brasil" | ORIGIN == "Colombia"| ORIGIN == "Uruguay")
```
## Recodificamos nuestras poblaciones de estudio en funcion del origen
```
data3$SOURCE[data3$ORIGIN == "AFRICA_hap"] <- "AFRICA"
data3$SOURCE[data3$ORIGIN == "EUROPE"] <- "EUROPA"
data3$SOURCE[data3$ORIGIN == "ASIA_hap"] <- "ASIA"
data3$SOURCE[data3$ORIGIN == "Peru"] <- "LATINO"
data3$SOURCE[data3$ORIGIN == "Uruguay"] <- "LATINO"
data3$SOURCE[data3$ORIGIN == "Brazil"] <- "LATINO"
data3$SOURCE[data3$ORIGIN == "Chile"] <- "LATINO"
data3$SOURCE[data3$ORIGIN == "Colombia"] <- "LATINO"
```
## Generamos el plot de las poblaciones HapMap3 (Europea, Africana y Asiatica) con respecto a la poblacion Latina
```
plot2 <- ggplot(data3, aes(PC1, PC2, shape = SOURCE, colour = pcntLatino)) + geom_point() + theme_bw() + ggtitle("Latino")
```

## Unimos los plots
```
plotted <- grid.arrange(plot1, plot2, nrow = 2)
```

## Exportamos los plots
```
ggsave(plotted, file = "pcntAncestriesAndPcs.png", height = 12, width = 12, units = "in")
```

## Cerramos R
```
q("no")
```
