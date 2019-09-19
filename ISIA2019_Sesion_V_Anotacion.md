# Anotacion y priorizacion de variantes mediante ANNOVAR 
	- **Author(s):** Ignacio Fernandez Mata, Cornelis Blauwendraat, Sara Bandres-Ciga
	- **Date Last Updated:** Sept. 2019
	
## Introduccion

ANNOVAR es una eficiente herramienta que utiliza informacion actualizada para anotar funcionalmente variantes geneticas detectadas a partir de distintos genomas (ej: genoma humano en las versiones hg18, hg19, hg38 asi como de otras especies como raton, levadura, mosca etc). 
Proporcionando una lista de variantes con chr, posicion inicial, posicion final, nucleotido en el genoma de referencia para cada una de esas variantes y nucleotido observado, este paquete puede llevar a cabo las siguientes anotaciones:

**Anotacion a nivel del gen:** 

Permite identificar si un SNP o variante estructural (ej. CNV) causa cambios a nivel proteico y cuales son los amino-acidos que se ven afectados. Los usuarios pueden libremente utilizar distintas datasets para nominar genes (RefSeq, UCSC, ENSEMBL, GENCODE).

**Anotacion a nivel de la region:**

Permite identificar variantes en regionas especificas del genoma (ej. regiones conservadas entre 44 especies, regiones que codifican para factores de transcripcion, loci encontrados a partir de GWAS, o cualquier otra anotacion basada en intervalos genomicos).

**Anotacion en funcion de un filtro especifico:**

Permite identificar variantes documentadas en distintas bases de datos (ej. si una determinada variante esta o no reportada en dbSNP, cual es la frecuencia alelica en 1000 genomes o en ExAC, asi como calcular los scores a partir de SIFT/PolyPhen/LRT/MutationTaster/MutationAssessor/FATHMM/MetaSVM/MetaLR, identificar variantes intergenicas mediante GERP++ score < 2, entre otros).

## Para comenzar, nos situamos en nuestro directorio..

```
cd /data/LNG/saraB/ANNO/
```

## OPCIONAL: Creamos una carpeta donde queremos almacenar nuestros outputs..

```
mkdir ANNOVAR_output
```
## OPCIONAL: Descargamos ANNOVAR

```
http://download.openbioinformatics.org/cgi-bin/annovar_download.cgi
```
Debemos registrarnos y a continuacion recibiremos un email con las instrucciones.

## OPCIONAL: Descomprimimos ANNOVAR
```
tar xvfz annovar.latest.tar.gz
```
Comprueba que el directorio bin/ contiene distintos Perl programs con sufijo *.pl

## OPCIONAL: Descargamos las bases de datos para las anotaciones
```
annotate_variation.pl -buildver hg18 -downdb -webfrom annovar refGene humandb/
annotate_variation.pl -buildver hg18 -downdb -webfrom annovar exac03 humandb/
annotate_variation.pl -buildver hg18 -downdb -webfrom annovar gerp++elem humandb/
annotate_variation.pl -buildver hg18 -downdb -webfrom annovar snp128 humandb/
annotate_variation.pl -buildver hg18 -downdb -webfrom annovar esp6500siv2_ea humandb/
annotate_variation.pl -buildver hg18 -downdb -webfrom annovar ljb26_all humandb/
```
## Convertimos *.VCF a *.avinput
```
convert2annovar.pl -format vcf4 SARA_ISIA2019/Sesion_V/Example.filtered.annot.vcf -allsample -withfreq > test.avinput

```
VCF es el formato gold standard que la mayoria de los investigadores utilizan. Normalmente partimos de un archivo *.VCF y lo convertimos en un input mas manejable *.avinput.

## Anotamos nuestro archivo *.avinput teniendo en cuenta hg18
```
table_annovar.pl test.avinput annovar/humandb/ -buildver hg18 -remove -protocol refGene,ljb26_all,exac03,gerp++elem,snp128,esp6500siv2_ea -operation g,f,f,f,f,f -nastring . -csvout -out myanno_E
```
Los exomas de este proyecto se han alineado con la version de constructo de genoma humano 18. 
Debemos mapear nuestras variantes de la misma manera.

El argumento table_annovar.pl es un comando utilizado para anotar tu input y generar un tab-delimited output que contiene columnas representativas de cada una de las anotaciones.
El argumento -operation le indica a ANNOVAR que operaciones utilizar para cada uno de los protocolos.
Los protocolos pueden ser "g" que indica "anotacion a nivel del gen" o "f" que significa "anotacion en funcion de un filtro especifico", entre otros.
El argumento -nastring me anadira un "." para aquellas variantes para las que no exista informacion.
El argumento -csvout me generara un output en formato csv facilmente leible por excel.
Para mas informacion sobre el tipo de filtros a utilizar -> http://annovar.openbioinformatics.org/en/latest/user-guide/startup/

## Output
El archivo output contiene multiples columnas. 
Cada una de las columnas corresponde a cada uno de los protocolos especificados en tu script.
Las columnas Func.refGene, Gene.refGene, GeneDetail.refGene, ExonicFunc.refGene, AAChange.refGene contienen anotaciones sobre como las mutaciones afectan a la estructura del gen y a la proteina. 
Las columnas ExAC* representan la frecuencia alelica en distintas sub-poblaciones dentro del Exome Aggregation Consortium.
Las columnas restantes contienen:
A) Predictores de patogenicidad para variantes de interes utilizando las herramientas SIFT, PolyPhen2 HDIV scores, PolyPhen2 HVAR scores, LRT scores, MutationTaster scores, MutationAssessor score, FATHMM scores,  CADD scores, 
B) Predictores de conservacion entre especies GERP++ scores,PhyloP scores y SiPhy scores.


# EJERCICIO PRACTICO

## OPCIONAL: Descargamos las bases de datos para las anotaciones
```
annotate_variation.pl -buildver hg18 -downdb -webfrom annovar refGene humandb/
annotate_variation.pl -buildver hg18 -downdb -webfrom annovar exac03 humandb/
annotate_variation.pl -buildver hg18 -downdb -webfrom annovar gerp++elem humandb/
annotate_variation.pl -buildver hg18 -downdb -webfrom annovar snp128 humandb/
annotate_variation.pl -buildver hg18 -downdb -webfrom annovar esp6500siv2_ea humandb/
annotate_variation.pl -buildver hg18 -downdb -webfrom annovar ljb26_all humandb/
```
## Convertimos *.VCF a *.avinput
```
convert2annovar.pl -format vcf4 SARA_ISIA2019/Sesion_V/Example.filtered.annot.vcf -allsample -withfreq > test.avinput

```
VCF es el formato gold standard que la mayoria de los investigadores utilizan. Normalmente partimos de un archivo *.VCF y lo convertimos en un input mas manejable *.avinput.

## Anotamos nuestro archivo *.avinput teniendo en cuenta hg18
```
table_annovar.pl test.avinput annovar/humandb/ -buildver hg18 -remove -protocol refGene,ljb26_all,exac03,gerp++elem,snp128,esp6500siv2_ea -operation g,f,f,f,f,f -nastring . -csvout -out ANOTACION_FAMILIA_C
```
Los exomas de este proyecto se han alineado con la version de constructo de genoma humano 18 
Debemos mapear nuestras variantes de la misma manera.

El argumento table_annovar.pl es un comando utilizado para anotar tu input y generar un tab-delimited output que contiene columnas representativas de cada una de las anotaciones.
El argumento -operation le indica a ANNOVAR que operaciones utilizar para cada uno de los protocolos.
Los protocolos pueden ser "g" que indica "anotacion a nivel del gen" o "f" que significa "anotacion en funcion de un filtro especifico", entre otros.
El argumento -nastring me anadira un "." para aquellas variantes para las que no exista informacion.
El argumento -csvout me generara un output en formato csv facilmente leible por excel.
Para mas informacion sobre el tipo de filtros a utilizar -> http://annovar.openbioinformatics.org/en/latest/user-guide/startup/

## Output
El archivo output contiene multiples columnas. 
Cada una de las columnas corresponde a cada uno de los protocolos especificados en tu script.
Las columnas Func.refGene, Gene.refGene, GeneDetail.refGene, ExonicFunc.refGene, AAChange.refGene contienen anotaciones sobre como las mutaciones afectan a la estructura del gen y a la proteina. 
Las columnas ExAC* representan la frecuencia alelica en distintas sub-poblaciones dentro del Exome Aggregation Consortium.
Las columnas restantes contienen:
A) Predictores de patogenicidad para variantes de interes utilizando las herramientas SIFT, PolyPhen2 HDIV scores, PolyPhen2 HVAR scores, LRT scores, MutationTaster scores, MutationAssessor score, FATHMM scores,  CADD scores, 
B) Predictores de conservacion entre especies GERP++ scores,PhyloP scores y SiPhy scores.
