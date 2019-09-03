# Anotacion y priorizacion de variantes mediante ANNOVAR 
	- **Author(s):** Sara Bandres-Ciga
	- **Date Last Updated:** Sept. 2019
	
## Introduccion

ANNOVAR es una eficiente herramienta que utiliza informacion actualizada para anotar funcionalmente variantes geneticas detectadas de distintos genomas (ej: genoma humano en las versiones hg18, hg19, hg38 asi como de otras especies como raton, levadura, mosca etc). 
Proporcionando una lista de variantes con chr, posicion inicial, posicion final, nucleotido en el genoma de referencia para esa variante y nucleotido observado, este package puede llevar a cabo las siguientes anotaciones:

**Anotacion a nivel del gen:** 
Permite identificar si un SNP o variante estructural (ej. CNV) cause cambios a nivel proteico y cuales son los amino-acidos que se ven afectados. Los usuarios pueden libremente utilizar distintas datasets para nominar genes (RefSeq, UCSC, ENSEMBL, GENCODE).
**Anotacion a nivel de la region:**
Permite identificar variantes en regionas especificas del genoma (ej. regiones conservadas entre 44 especies, or example, conserved regions among 44 species, regiones que codifican para factores de transcripcion, loci encontrados a partir de GWAS hits, o cualquier otra anotacion basada en intervalos genomicos.
**Anotacion en funcion de un filtro especifico:**
Permite identificar variantes documentadas en distintas bases de datos (ej. si una variante esta reportada en dbSNP, cual es la frecuencia alelica en 1000 genomes, ExAC, calcular los scores de SIFT/PolyPhen/LRT/MutationTaster/MutationAssessor/FATHMM/MetaSVM/MetaLR, identificar variantes intergenicas mediante GERP++ score < 2, entre otros).

## Nos situamos en nuestro directorio..

```
cd /data/LNG/saraB/ANNO/
```

## Creamos una carpeta donde queremos almacenar nuestros outputs..

```
mkdir ANNOVAR_output
```
## Descargar ANNOVAR

```
http://download.openbioinformatics.org/cgi-bin/annovar_download.cgi
```
Debemos registrarnos y a continuacion recibiremos un email con las instrucciones.

## Descomprimir ANNOVAR
```
tar xvfz annovar.latest.tar.gz
```
Comprueba que el directorio bin/ contiene distintos Perl programs con sufijo .pl

## Descargar bases de datos para las anotaciones
```
annotate_variation.pl -buildver hg19 -downdb -webfrom annovar refGene humandb/
annotate_variation.pl -buildver hg19 -downdb -webfrom annovar exac03 humandb/ 
annotate_variation.pl -buildver hg19 -downdb -webfrom annovar avsnp147 humandb/ 
annotate_variation.pl -buildver hg19 -downdb -webfrom annovar dbnsfp30a humandb/
```
## Convertir *.VCF a *.avinput
```
convert2annovar.pl -format vcf4 /data/LNG/saraB/ANNO/ANNOVAR_input/FILTERED.ataxia_chr1.WES.vcf > /data/LNG/saraB/ANNO/ANNOVAR_input/FILTERED.ataxia_chr1.WES.avinput
```
VCF es el formato gold standard que la mayoria de los investigadores utilizan. A veces partimos de un archivo *.VCF pero queremos convertirlo en un input mas manejable *.avinput.

## Anotamos nuestro archivo *.avinput teniendo en cuenta hg19
```
table_annovar.pl /data/LNG/saraB/ANNO/ANNOVAR_input/FILTERED.ataxia_chr1.WES.avinput ANNOVAR_DATA/hg19 -buildver hg19 \
```
Los exomas de este proyecto se han alineado con la version de constructo de genoma humano 19. 
Debemos mapear nuestras variantes de la misma manera.
table_annovar.pl es un comando utilizado para anotar tu input y generar un tab-delimited output que contiene columnas representando cada una de las anotaciones.

## Especificamos numero de threads
```
--thread 16 \
```

## Especificamos el nombre de nuestro output
```
-out /data/LNG/saraB/ANNO/ANNOVAR_output/FILTERED.ataxia_chr1.WES \
```
## Especificamos bases de datos y tipo de anotacion
```
-remove -protocol refGene,ensGene,ljb26_all,gnomad211_genome,exac03,avsnp147,dbnsfp30a -operation g,g,f,f,f,f,f -nastring . -csvout
```
El argumento -operation le indica a ANNOVAR que operaciones utilizar para cada uno de los protocolos.
Los protocolos pueden ser "g" que significa "anotacion a nivel del gen", "f" que significa "anotacion en funcion de un filtro especifico", entre otros.
El comando nastring me anadira un . para aquellas variantes sin informacion.
El comando csvout me generara un output en formato csv facilmente leible por excel.

## Anotamos variantes en splicing
```
-arg '-splicing 15',,, \
```
## Output
El archivo output contiene multiples columnas. 
Cada una de las columnas corresponde a cada uno de los protocolos especificados en tu script.
La columna Func.refGene, Gene.refGene, GeneDetail.refGene, ExonicFunc.refGene, AAChange.refGene contiene anotaciones sobre como las mutaciones afectan a la estructura del gen y a la proteina. 
Las columnas ExAC* representan la frecuencia alelica en distintas sub-poblaciones dentro del Exome Aggregation Consortium, mientras que avsnp147 hace referencia al rs ID de cada variante en la version 147 de dbSNP.
Las columnas restantes contienen predictores de patogenicidad para variantes no-sinonimas utilizando las herramientas SIFT, PolyPhen2 HDIV scores, PolyPhen2 HVAR scores, LRT scores, MutationTaster scores, MutationAssessor score, FATHMM scores, GERP++ scores, CADD scores, DANN scores, PhyloP scores y SiPhy scores.
