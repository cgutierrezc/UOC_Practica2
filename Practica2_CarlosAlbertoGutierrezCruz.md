---
output:
  pdf_document: default
  html_document: default
---
Practica 2 Limpieza y Validacion de Datos
================
Carlos A Gutierrez Cruz
10 de junio de 2018

`{r setup, include=FALSE} knitr::opts_chunk$set(echo = TRUE)`

Presentación
------------

En esta práctica se elabora un caso práctico orientado a aprender a identificar los datos relevantes para un proyecto analítico y usar las herramientas de integración, limpieza, validación y análisis de las mismas.

Competencias
------------

En esta práctica se desarrollan las siguientes competencias del Máster de Data Science:

-   Capacidad de analizar un problema en el nivel de abstracción adecuado a cada situación y aplicar las habilidades y conocimientos adquiridos para abordarlo y resolverlo.
-   Capacidad para aplicar las técnicas específicas de tratamiento de datos (integración, transformación, limpieza y validación) para su posterior análisis.

Objetivos
---------

Los objetivos concretos de esta práctica son:

-   Aprender a aplicar los conocimientos adquiridos y su capacidad de resolución de problemas en entornos nuevos o poco conocidos dentro de contextos más amplios o multidisciplinares.
-   Saber identificar los datos relevantes y los tratamientos necesarios (integración, limpieza y validación) para llevar a cabo un proyecto analítico.
-   Aprender a analizar los datos adecuadamente para abordar la información contenida en los datos.
-   Identificar la mejor representación de los resultados para aportar conclusiones sobre el problema planteado en el proceso analítico.
-   Actuar con los principios éticos y legales relacionados con la manipulación de datos en función del ámbito de aplicación.
-   Desarrollar las habilidades de aprendizaje que les permitan continuar

Desarrollo de la Práctica
-------------------------

### 1. Descripción del dataset. ¿Por qué es importante y qué pregunta/problema pretende responder?

Para el desarrollo de la práctica se ha escogido un conjunto de datos (dataset) del sitio de Kaggle con la siguiente dirección. "<https://www.kaggle.com/checoalejandro/autos-consumo-gasolina-mexico/data>"

La intención de haber escogido este dataset fué primero escoger un dataset de algo relacionado con México que es mi país de origen y en segundo porque es conocido que la Ciudad de México es una de las ciudades más pobladas del mundo y que uno de sus principales problemas es la contaminación originados principalmente por los vehículos automotores que circulan diariamente en la ciudad.

Bajo el supuesto de que el dataset es una muestra aleatoria y representativa sobre el consumo de gasolina de los vehículos automotores de modelos 2011-2018 en México puede ejemplificar perfectamente el uso de técnicas con programación en R para el análisis de datos.

El dataset se compone de una muestra de 4617 observaciones y 18 variables que acontinuación se enlistan y describen.

-   Marca - Marca frabricante del vehiculo
-   Submarca - Sublinea
-   Modelo - Año del vehiculo
-   Trans. - Tipo de transmisión (manual, automática, CVT)
-   Comb. - Tipo de combustible (Diesel, Gasolina)
-   Cilindros - Número de cilindros del vehiculo (4, 6, 8)
-   Potencia (HP) - Potencia del motor en caballos de fuerza (HP)
-   Tamaño (L) - Tamaño del motor (1.5, 1.8, 2.0)
-   Categoría - Auto compacto, lujo, deportivo, SUV
-   R. Ciudad (km/l) - Rendimiento en Ciudad kilómetros por litro
-   R. Carr. (km/l) - Rendimiento en Carretera kilólmetros por litro
-   R. Comb. (km/l) - Rendimiento combinado (ciudad/carretera)
-   R. Ajust. (km/l) - Rendimiento ajustado
-   CO2(g/km) - Cantidad de emisión de Dioxido de Carbono
-   NOx (g/1000km) - Cantidad de Oxigeno de Nitrogeno
-   Calificación Gas Ef. Inv. - Categorización de uso de efectivo de combustible
-   Calificación Contam. Aire - Categorización sobre el nivel de contaminación.

Dado la información anterior es interesante plantearse algunas preguntas como:

Si lo vehiculos cubren las normas de emisión planteadas El nivel de emisión de los vehiculos tiene relación con el modelo El rendimiento en el consumo de combustible esta relacionado con el modelo o tipo de vehiculo.

Se pueden hacer muchas preguntas al respecto, pero lo importante del ejercicio es mostrar la utilidad que tienen las técnicas y herramientas dentro del procesamiento y análisis de los datos.

### 2. Integración y selección de los datos de interés a analizar.

Procederemos a cargar el archivo "Consumo Gasolina Autos Ene 2018.csv" y haremos un análisis exploratorio para observar el tipo de información contenida que pueda ser de utilidad.

``` {r}
dir()
setwd("D:/UOC/02 Tipología y ciclo de vida de los datos/Practica 2 Limpieza y Validacion de Datos/Practica2")
# Carga de datos
data <- read.csv("Consumo Gasolina Autos Ene 2018.csv", header=TRUE)

attach(data)

summary(data)

# Tipo de datos de cada variable
sapply(data, function(x) class(x))

table (data[,6])
```

Dada la información mostrada, no sería útil considerar los vehiculos con uso de combustible diesel ya que por el numero de observaciones que se tienen no son significantes, así mismo nos centraremos en los autos para lo cual no consideraremos en este trabajo las camionetas o vehiculos utilitarios ya que ademas de estar sujetos a otros niveles ambientales, tienen un rendimiento de combustible mucho menor a los autos a fin de evitar mucha variabilidad y dispersion de los datos durante el análisis.

``` {r}
datos2 <- subset(data, Comb.=="Gasolina" & Categoría != "CAMIONETAS DE USO MULTIPLE (SUV)")
attach(datos2)
summary(datos2)
```

### 3. Limpieza de los datos.

#### 3.1. ¿Los datos contienen ceros o elementos vacíos? ¿Cómo gestionarías cada uno de estos casos?

las siguientes instrucciones en R nos permitiran conocer si existen ceros o elementos vacios.

``` {r}
sapply(datos2, function(x) sum(is.na(x)))
```

Lo cual nos permite apreciar que no existen dentro de las variables de la nuevo dataset(datos2) elementos vacios.

Si hubieran existido elementos vacios dentro de variables se pudieran haber tratado de la siguiente forma.

-   Eliminar los registros si fueran pocas las observaciones con elementos vacios
-   Completar la informacion con el promedio de las catagorias de la variable si es que no hay mucha variabilidad en los datos.
-   Imputacion de valores basado en valores proximos

#### 3.2. Identificación y tratamiento de valores extremos.

Los siguientes diagramas en R para analizar variables de interes nos ayudan a identificar outliers o valores extremos.

``` {r}

#diagramas de boxplot

par(mfrow=c(1,1))
    
boxplot(CO2.g.km.~ Modelo, data=datos2, main="Emision de CO2 por modelo", xlab="Modelo", ylab="CO2 g/km" )

boxplot(NOx..g.1000km.~ Modelo, data=datos2,main="Emision de NOx por modelo", xlab="Modelo", ylab="NOx g/1000 km" )

boxplot(R..Comb...km.l. ~ Cilindros ,data=datos2,main="Rendimiento Combustible Combinado (Ciudad/Carr.) por Num. de Cilindros", xlab="Numero de Cilindros", ylab="Rendimiento Combustible km/l")

boxplot(R..Comb...km.l. ~ Modelo, data=datos2,main="Rendimiento Combustible Combinado (Ciudad/Carr.) por Modelo", xlab="Modelo", ylab="Rendimiento Combustible km/l")

#grabamos los datos a utilizar para el analisis
write.csv(datos2, "Consumo Gasolina Autos Ene 2018_Nuevo.csv")
```

### 4. Análisis de los datos.

#### 4.1. Selección de los grupos de datos que se quieren analizar/comparar (planificación de los análisis a aplicar).

Las variables o grupos de datos para analizar y comparar seran:

-   Modelo del vehiculo
-   Cilindros
-   Rendimientos de combustible
-   Niveles de emisión

``` {r}
#Resumen de variables de interes
#Agrupación por Modelo
table(Modelo)
#Agrupación por Num. cilindros
table(datos2$Cilindros)

#Resumen de medidas de tendencia central
summary(datos2$R..Comb...km.l.)
summary(datos2$CO2.g.km.)
summary(datos2$NOx..g.1000km.)

#Grupos por tipo num de cilindros
datos2.hasta4 <- subset(datos2, Cilindros <= 4)
datos2.mayor4 <- subset(datos2, Cilindros > 4)

```

#### 4.2. Comprobación de la normalidad y homogeneidad de la varianza.

Para la comprobacion de la normalidad existen dos pruebas importantes Kolmogorov-Smirnov y Shapiro-Wilk. Dado que el numero de observaciones es mayor a 50 usaremos la de Kolmogorov-Smirnov

A continuacin se muestra un ejemplo las variables relacionadas con los niveles de emision CO2 y NOx

``` {r}
#Prueba de Normalidad

ks.test(CO2.g.km., "pnorm", mean=mean(CO2.g.km., sd=sd(CO2.g.km.)))


ks.test(NOx..g.1000km. , "pnorm", mean=mean(NOx..g.1000km.), sd=sd(NOx..g.1000km.))

#Prueba de F para igualdad de varianzas

var.test(datos2.hasta4$CO2.g.km., datos2.mayor4$CO2.g.km.)
```

El resultado de p-value en la prueba de Kolmogorov-Smirnov es tan pequeño nos ayuda a determinar que se rechaza la hipótesis alternativa y se aceptaría la hipótesis nula de que las observacioes provienen de una distrbución normal.

La prueba de F tambien nos ayuda a determinar la igualdad de las varianzas para los grupos de emisiones para automoviles de menos de 4 cilindros con los mayores a 4 cilindros

Lo cual también puede ejemplificarse de forma grafica

``` {r}
par(mfrow=c(2,2))
qqnorm(CO2.g.km., main = "Q-Q Norm Emisiones CO2")
qqline(CO2.g.km., col="red")
hist(CO2.g.km., main = "Histograma Emisiones CO2")
```

#### 4.3. Aplicación de pruebas estadísticas para comparar los grupos de datos.

En función de los datos y el objetivo del estudio, aplicar pruebas de contraste de hipótesis, correlaciones, regresiones, etc.

A continuacion vamos a comprobar que los vehiculos de menos de 4 cilindros cumplen con la norma de niveles de emision de Oxido de Nitrogeno NOx. La cual es de 21 g/1000km. Para ello nos apoyaremos de la prueba de t donde las hipotesis a comprobar son H0 La emision de NOx cumple al menos con la norma Ha La emisi\[on de Nox es mayor a la norma\]

``` {r}

#Prueba de t para una muestra con intervalo de confianza al 95%

t.test(datos2.hasta4$NOx..g.1000km., alternative = "greater", mu=21)

t.test(datos2.mayor4$NOx..g.1000km., alternative = "greater", mu=21)

```

Estadisticamente se puede decir que los vehiculos con mayor a 4 cilindros cumplen con la norma.

Por ultimo realizaremos una relacion si el rendimiento de combustible tiene relacion con el año del modelo vehiculo y numero de cilindros. Para ellos revisaremos los coeficientes de correlacion y modelos de regresion

``` {r}

cor(datos2$Modelo, datos2$R..Comb...km.l.)

cor(datos2$Cilindros, datos2$R..Comb...km.l.)

pairs(~datos2$Cilindros+datos2$Modelo+datos2$R..Comb...km.l.)

summary(lm(datos2$R..Comb...km.l.~datos2$Modelo+datos2$Cilindros))
```

Conforme al resultado de la funcion de regresion lm(), El rendimiento de combustible de los vehiculos tiene una significante relacion con las varibales modelo y numero de cilindros. Ademas el coeficiente R2 nos muestra que cerca del 67% de los datos quedarian explicados por el modelo.

### 5. Representación de los resultados a partir de tablas y gráficas.

``` {r}

ryx <-lm(datos2$R..Comb...km.l.~datos2$Tamaño..L.)

linea <- ryx$coefficients[1]+(ryx$coefficients[2]*datos2$Tamaño..L.)
plot(datos2$Tamaño..L.,datos2$R..Comb...km.l.,  xlab = "Tamaño", ylab="Rendimiento km/l")

lines(linea, col=4)
```

La grafica muestra el modelo lineal para la relación que hay entre el rendimiento de comustible dependiendo el tamaño del motor (1.5litros, 2.0 litros, etc)

### 6. Resolución del problema. A partir de los resultados obtenidos, ¿cuáles son las conclusiones? ¿Los resultados permiten responder al problema?

El caso práctico nos permitió mostrar la aplicación práctica de las técnicas y herramientas para el procesamiento y analisis de datos.

Basado en el dataset nos permitio ejemplicar que no existía una diferencia en cuanto a los nivele de emisión CO2 o Nox independientemente del número de cilindros del motor

También nos permitio ver estadísticamente que el rendimientos de combustible de los vehiculos tomados de la muestra tienen una relación significativa con el modelo y numero de cilindros de la maquina.

Por último, también se puede decir que estadísticamente, los vehiculos con numero de cilindros mayor a 4 cumplieron con la norma en el nivel de emisión, lo que hace suponer que a pesar de que son maquinas mas grande nos hace suponer que trabajan a mayor estabilidad que los motores con menos cilindros.

Dado lo anterior siempre se puede decir que este tipo de conclusiones estan basadas en la muestra utilizada y para otro tipo de prueba similar siempre es importante llevar acabo una planeacióny diseño para la recolección de los datos sobre el problema a resolver o analizar.
