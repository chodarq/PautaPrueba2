# PautaPrueba2
## Pregunta 5

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
```
Puesto que las desviaciones de cada componente estan en $sdev, obtenemos la varianza como porcentaje con una simple fórmula:
```R
varianzas<- 100*winepca$sdev^2/sum(winepca$sdev^2)
```

2) ¿De cuántos componentes resultó su nueva matriz? Justifique

Lo único que tenian que definir es si querían explicar el 80%, 90% o 99% de su varianza con las nuevas componentes. Como mencionamos en clases, no hay una "regla de pulgar" pero idealmente es conveniente elegir un conjunto de variables que expliquen en conjunto mas del 90% de la varianza total, en este caso, las primeras 7 explican 89.4% de la varianza y las primeras 8 un 92.04

3) ¿Qué puede decir de las observaciones y las variables en el nuevo espacio multidimensional?

Si consideramos las primeras dos componentes, podemos graficar las observaciones en ese espacio con:
```R
plot(winepca$x[,1],winepca$x[,2],col=as.factor(wine$Cultivar)) # make a scatterplot
```
![](https://github.com/chodarq/PautaPrueba2/blob/master/gafico1.png)

Lo que el gráfico nos muestra es que existe una buena separación de las variables originales en este nuevo espacio, considerando que esas dos primeras componentes explican algo mas del 50% de la varianza total. Sin embargo no sabemos cual de las variables originales son las responsables de esta separación. Para eso necesitamos proyectar las variables originales en el nuevo espacio con la función <i>biplot</i>. El gráfico nos muestra que a pesar de que los grupos se separan, no es del todo claro que variable pesa mas que otra, para estas dos dimensiones. A priori se podria decir que los fenoles no flavonoides y la alcalinidad de la ceniza aportan a la componente principal 1 y las prolinas a la componente 2. Para tener mas certeza se necesitaria proyectar con las respantes variables.

```R
biplot(winepca)
```
![](https://github.com/chodarq/PautaPrueba2/blob/master/grafico2.png)

4) Efectúe con los mismos datos un análisis de MDS. Compare y comente el resultado obtenido con el del PCA. (Considere 2 dimensiones)
Para efectuar un análisis MDS debemos tener la data escalada y ademas como matrix, no como dataframe. Para esto:
```R
winedist<-dist(winescaled,method="euclidean",upper=T,diag=T)
winedist<-as.matrix(winedist) #convertir a matriz
wineMDS<- cmdscale(winedist,k=2) # k=2 dos dimensiones.
```
El gráfico podían verlo simplemente con el comando <i>plot</i> o usar <i>ggplot</i> como lo vimos en clases. Lo importante es que al graficar en dos dimensiones es que el resultado es muy similar al obtenido con el PCA, si es que en este usaron la distancia euclidiana. Esto porque al usar euclidiana en el PCA la matriz de covarianza se transforma en la de distancias, que es equivalente a la de PCA. PCA minimiza componentes manteniendo la covarianza, MDS minimza manteniendo distancia. Si estas matrices son equivalentes, los resultados son similares. 
```R
plot(wineMDS,col=as.factor(wine$Cultivar))
# con ggplot
g <- ggplot(data.frame(wineMDS[,1:2], wine2), 
            aes(wineMDS[,2], wineMDS[,1],
                label = rownames(wine2), group=as.factor(wine$Cultivar)))

g + geom_text(aes(color = as.factor(wine$Cultivar)) , 
              angle=0, show.legend = T, nudge_x = 0.05, nudge_y = 0.15) + 
  ggtitle("Multi-dimensional Scaling on Wine Data") +
  geom_point() + theme_classic() + 
  theme(legend.position = "bottom", legend.title = 
          element_text(size=10, face="bold"), 
        legend.text = element_text(size=10, face="bold"))
``` 
![](https://github.com/chodarq/PautaPrueba2/blob/master/grafico3.png)
![](https://github.com/chodarq/PautaPrueba2/blob/master/grafico4.png)

## Pregunta 6

A partir del mismo archivo wine.csv, efectúe los siguientes análisis, justificando en cada caso sus supuestos al momento de efectuar la agrupación de los datos.

a)Clúster jerárquico. ¿En cuántos grupos definiría Ud. el clúster?
Aquí no hay una regla, es a ojo de quien hace el análisis. Sin embrago, los datos que tenemos permiten definir bien 4 grupos, usando los linkages Promedio y Completo. Lo único obligatorio, el escalado de los datos.
```R
winedist2<-dist(winescaled,method="euclidean",upper=T,diag=T) 
winehc1<-hclust(winedist2,method="average")
winehc2<-hclust(winedist2,method="single")
winehc3<-hclust(winedist2,method="complete")
plot(winehc1)
plot(winehc2)
plot(winehc3)
```
![](https://github.com/chodarq/PautaPrueba2/blob/master/grafico6.png)
![](https://github.com/chodarq/PautaPrueba2/blob/master/grafico7.png)
![](https://github.com/chodarq/PautaPrueba2/blob/master/grafico8.png)

b)K-means con 3 grupos. Grafique 2 pares de variables, justifique cuales, e interprete los resultados.
c)Repita el análisis con K=5 y determine que agrupamiento es mejor
