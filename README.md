# PautaPrueba2
Pauta Prueba 2 - Bioestadistica DCSAV

El archivo wine.csv contiene 13 parámetros fisicoquímicos asociados a muestras de vino de tres cultivares distintos: alcohol, ac. málico, ceniza, alcalinidad de la ceniza, magnesio, contenido fenoles, flavonoides, fenoles no flavonoides, proantocianidinas, intensidad color, tinte, OD280_315 y prolinas. Indicación: las tres librerias fuera de las básicas que vienen con R y que podría necesitar, pero no son esenciales son ggplot, factoextra y cluster. Para cargar el archivo ejecute:
```R
wine<-read.csv("https://raw.githubusercontent.com/chodarq/Wine-data-for-PCA-exercise/master/wine.csv",header=T)
```
1) Efectúe una reducción de datos utilizando la técnica de PCA, explicando claramente las decisiones que utilizó y los supuestos asumidos. Utilice los gráficos que considere adecuados explicando claramente lo que se ve en ellos.

Existen varias opciones que podían usar. La que vimos en clases fue <i>prcomp</i>. Como los datos provienen de mediciones hechas en distintas escalas, es necesario escalar las variables. El código entonces queda: 
```R
wine<-read.csv("wine.csv",header=T) #Asignacion de datos a frame
winescaled<-scale(wine) #escalado
winepca<-prcomp(winescaled) #PCA
```
