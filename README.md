# PautaPrueba2
Pauta Prueba 2 - Bioestadistica DCSAV

El archivo wine.csv contiene 13 parámetros fisicoquímicos asociados a muestras de vino de tres cultivares distintos: alcohol, ac. málico, ceniza, alcalinidad de la ceniza, magnesio, contenido fenoles, flavonoides, fenoles no flavonoides, proantocianidinas, intensidad color, tinte, OD280_315 y prolinas. Indicación: las tres librerias fuera de las básicas que vienen con R y que podría necesitar, pero no son esenciales son ggplot, factoextra y cluster. Para cargar el archivo ejecute:
```R
wine<-read.csv("https://raw.githubusercontent.com/chodarq/Wine-data-for-PCA-exercise/master/wine.csv",header=T)
```
1) Efectúe una reducción de datos utilizando la técnica de PCA, explicando claramente las decisiones que utilizó y los supuestos asumidos. Utilice los gráficos que considere adecuados explicando claramente lo que se ve en ellos.

Existen varias opciones que podían usar. La que vimos en clases fue <i>prcomp</i> y <i>princomp</i>. Consideraciones que hay que tener:
- Los datos tienen distintas escalas, por lo que hay que escalar. Fundamental y será evaluado
- La variable "Cultivar" es discreta y no debe incorporarse al modelo. Se evalaurá en funcion de los resultados y explicaciones posteriores.
- Las variables, por definición, deben presentar algun grado de correlación. Las no correlacionadas no aportan al análisis. Será evaluado.
- Los datos estan o no muy correlacionados. Esto es opcional, a veces se hace a veces no, dependiendo del resultado y del ojo del estadistico. No consider al evaluar

```R
wine2<-wine[,c(2:14)] # Eliminar la variable cultivar del modelo
cor(wine2) # para explorar las correlaciones podrian haber usado
pairs(wine2) # Usando la libreria 
winescaled<-scale(wine2) #escalado
winepca<-prcomp(winescaled) # PCA

# Puesto que las desviaciones de cada componente estan en $sdev,
# obtenemos la varianza como porcentaje con una simple fórmula:

varianzas<- 100*winepca$sdev^2/sum(winepca$sdev^2)
```
2) ¿De cuántos componentes resultó su nueva matriz? Justifique

Lo único que tenian que definir es si querían explicar el 80%, 90% o 99% de su varianza con las nuevas componentes. Como mencionamos en clases, no hay una "regla de pulgar" pero idealmente es conveniente elegir un conjunto de variables que expliquen en conjunto mas del 90% de la varianza total, en este caso, las primeras 7 explican 89.4% de la varianza y las primeras 8 un 92.04

3) ¿Qué puede decir de las observaciones y las variables en el nuevo espacio multidimensional?
Si consideramos las primeras dos componentes:
```R
plot(winepca$x[,1],winepca$x[,2],col=as.factor(wine$Cultivar)) # make a scatterplot
```
![]https://github.com/chodarq/PautaPrueba2/blob/master/gafico1.png?raw=true
