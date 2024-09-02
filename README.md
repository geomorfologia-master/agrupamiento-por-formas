Prácticas de aula 3 (PA03). Agrupar provincias según su formas
predominantes<small><br>Geomorfología (GEO-114)<br>Universidad Autónoma
de Santo Domingo (UASD)<br>Semestre 2024-02</small>
================
El Tali
2024-09-02

Versión HTML (quizá más legible),
[aquí](https://geomorfologia-master.github.io/agrupamiento-por-formas/README.html)

# Fecha/hora de entrega

**03 de septiembre de 2024, 7:59 pm.**

# Introducción

Qué son los DEM, cómo se obtienen, y qué importancia tienen (puedes
citar a Hengl and Reuter (2008)).

Qué son las técnicas de clasificación de formas del terreno (e.g. los
geomórfonos), que se basan en DEM, citando a Hengl and Reuter (2008),
Jasiewicz and Stepinski (2013)

Qué son las técnicas de análisis de agrupamiento (UPGMA, etc), qué
utilidad tienen Borcard, Gillet, and Legendre (2018),

Una vez clasificados los elementos, cómo estas clasificaciones podrían
ayudar a caracterizar más fácilmente y con técnicas estadísticas,
unidades convencionales o físicas (Triola (2012) parte estadística).

Utilidad de la clasificación del relieve, qué problema se resuelve con
ellos.

Cómo se pueden agrupar unidades como provincias, regiones o municipios,
en función de la distribución porcentual de formas del terreno usando
UPGMA, usando los datos de Martínez-Batlle (2022).

Qué aportes dan para el conjunto de la ciencia.

# Ejercicio. Clasificar 6 provincias dominicanas según sus formas predominantes por el método de agrupamiento jerárquico aglomerativo promedio no ponderado (UPGMA)

## Objetivo

## Planteamiento del Problema

Usando los datos de Martínez-Batlle (2022) …

## Obtención de los datos

1.  Cargar datos fuente. Los datos fuente proceden de Martínez-Batlle
    (2022).

Primero, es necesario cargar paquetes.

``` r
library(tidyverse)
library(sf)
library(tmap)
```

Luego leemos los datos.

``` r
prov <- st_read('data/provincias-geomorfonos.gpkg', quiet = T) %>% 
  rename(nombre = hex_id,
         llanura = flat, pico = peak,
         `cresta\n(interfluvio no inclinado)` = ridge, hombrera = shoulder,
         `gajo\n(interfluvio inclinado)` = spur, vertiente = slope,
         vaguada = hollow, piedemonte = footslope, valle = valley,
         `depresión/sima` = pit)
# Comprobar 100%
# prov %>% st_drop_geometry() %>% select(-base, -nombre) %>% rowSums()
```

Una representación cartográfica te ayudará a ver el conjunto en el país.

``` r
prov %>% select(-matches('base')) %>%
  pivot_longer(names_to = 'variable', values_to = 'value', -c(geom, nombre)) %>%
  tm_shape() +
  tm_fill(col='value', palette = "-BrBG", size = 0.1, style = 'fisher', n = 3,
          legend.is.portrait = T) +
  tm_borders(col = 'grey15', lwd = 0.3) +
  tm_facets(by = "variable", ncol = 3, nrow = 4, free.coords = FALSE, free.scales = TRUE) +
  tm_layout(
    panel.label.size = 1.5,
    panel.label.height = 2,
    legend.height = 0.3,
    legend.text.color = 'black',
    legend.width = 0.95,
    legend.text.size = 1, 
    legend.title.size = 0.001,
    legend.position = c('right', 'bottom'), 
    legend.frame = FALSE)
```

<img src="README_files/figure-gfm/unnamed-chunk-5-1.png" width="100%" />

2.  **Creación de los 10 conjuntos** (reserva el conjunto 1 al Tali). Se
    han creado diez conjuntos de seis provincias elegidas al azar
    utilizando sus nombres con el siguiente código de R (si no lo ves,
    presiona el botón `Show`).

``` r
set.seed(123)
replicas <- replicate(10, sample(prov$nombre, 6))
df <- data.frame(Conjunto = 1:10, t(replicas))
df %>%
  unite("Provincias asignadas", X1:X6, sep = ", ") %>% 
  knitr::kable()
```

| Conjunto | Provincias asignadas                                                                             |
|---------:|:-------------------------------------------------------------------------------------------------|
|        1 | SAN JOSÉ DE OCOA, MONTE CRISTI, HERMANAS MIRABAL, MARÍA TRINIDAD SÁNCHEZ, BAORUCO, INDEPENDENCIA |
|        2 | PUERTO PLATA, SAN JUAN, LA ALTAGRACIA, DAJABÓN, SAMANÁ, MARÍA TRINIDAD SÁNCHEZ                   |
|        3 | SAN JUAN, SANTIAGO, SANTIAGO RODRÍGUEZ, VALVERDE, DAJABÓN, HERMANAS MIRABAL                      |
|        4 | VALVERDE, SANTIAGO, MONSEÑOR NOUEL, SAN JOSÉ DE OCOA, ESPAILLAT, BAORUCO                         |
|        5 | EL SEIBO, SANTIAGO RODRÍGUEZ, ELÍAS PIÑA, INDEPENDENCIA, ESPAILLAT, HERMANAS MIRABAL             |
|        6 | BARAHONA, MARÍA TRINIDAD SÁNCHEZ, PERAVIA, LA ALTAGRACIA, ELÍAS PIÑA, SAN CRISTÓBAL              |
|        7 | LA ROMANA, MONTE CRISTI, INDEPENDENCIA, LA VEGA, ELÍAS PIÑA, ESPAILLAT                           |
|        8 | ESPAILLAT, INDEPENDENCIA, SAN PEDRO DE MACORÍS, VALVERDE, MONSEÑOR NOUEL, SAN CRISTÓBAL          |
|        9 | ELÍAS PIÑA, SAN CRISTÓBAL, VALVERDE, DUARTE, SANTIAGO, AZUA                                      |
|       10 | MONTE PLATA, DAJABÓN, EL SEIBO, LA ROMANA, LA VEGA, PUERTO PLATA                                 |

3.  **Presentación de los datos crudos de cada uno de los diez
    conjuntos**. Esta matriz contiene el porcentaje de la superifice
    provincial ocupada por cada forma del terreno. Con esta matriz
    podrás hacer cálculos de distancias.

``` r
conjuntos_l <- sapply(1:ncol(replicas),
       function(x)
         prov %>%
         select(-base) %>% 
         filter(nombre %in% replicas[, x]) %>% st_drop_geometry(),
       simplify = F) %>%
  setNames(paste0('Conjunto ', 1:10))
```

``` r
# Imprimir las tablas
for (tabla in 1:length(conjuntos_l)) {
  cat('Conjunto', tabla, "\n")
  print(kableExtra::kable(
    conjuntos_l[[tabla]],
    format = ifelse(grepl('gfm', output_format), 'markdown', 'html'),
    digits = 2))
  cat("\n\n")
}
```

Conjunto 1

\|nombre \| llanura\| depresión/sima\| pico\| cresta (interfluvio no
inclinado)\| hombrera\| gajo (interfluvio inclinado)\| vertiente\|
vaguada\| piedemonte\| valle\|
\|:———————-\|——-:\|————–:\|—-:\|——————————–:\|——–:\|—————————:\|———:\|——-:\|———-:\|—–:\|
\|BAORUCO \| 28.09\| 0.18\| 0.61\| 6.66\| 1.54\| 12.39\| 29.65\| 10.87\|
2.60\| 7.41\| \|INDEPENDENCIA \| 20.53\| 0.26\| 0.63\| 6.41\| 1.46\|
13.12\| 37.19\| 10.55\| 3.16\| 6.69\| \|MARÍA TRINIDAD SÁNCHEZ \|
32.33\| 0.45\| 0.83\| 7.86\| 4.40\| 9.28\| 23.75\| 7.90\| 5.06\| 8.12\|
\|MONTE CRISTI \| 43.73\| 0.07\| 0.54\| 5.62\| 4.67\| 7.16\| 20.45\|
6.02\| 6.81\| 4.93\| \|HERMANAS MIRABAL \| 21.30\| 0.78\| 1.36\| 9.58\|
4.72\| 11.48\| 24.53\| 10.84\| 4.28\| 11.12\| \|SAN JOSÉ DE OCOA \|
1.26\| 0.61\| 1.09\| 12.12\| 0.23\| 19.83\| 32.97\| 17.98\| 0.61\|
13.31\|

Conjunto 2

\|nombre \| llanura\| depresión/sima\| pico\| cresta (interfluvio no
inclinado)\| hombrera\| gajo (interfluvio inclinado)\| vertiente\|
vaguada\| piedemonte\| valle\|
\|:———————-\|——-:\|————–:\|—-:\|——————————–:\|——–:\|—————————:\|———:\|——-:\|———-:\|—–:\|
\|DAJABÓN \| 12.17\| 0.39\| 1.02\| 9.63\| 3.83\| 14.02\| 30.65\| 12.87\|
4.98\| 10.44\| \|LA ALTAGRACIA \| 55.35\| 0.16\| 0.39\| 3.99\| 5.75\|
4.91\| 14.90\| 4.23\| 5.78\| 4.53\| \|MARÍA TRINIDAD SÁNCHEZ \| 32.33\|
0.45\| 0.83\| 7.86\| 4.40\| 9.28\| 23.75\| 7.90\| 5.06\| 8.12\| \|PUERTO
PLATA \| 9.65\| 0.39\| 1.29\| 10.45\| 1.42\| 14.86\| 34.59\| 13.40\|
3.30\| 10.65\| \|SAMANÁ \| 15.63\| 1.61\| 2.20\| 11.57\| 1.83\| 13.60\|
27.97\| 11.74\| 2.76\| 11.09\| \|SAN JUAN \| 8.78\| 0.28\| 0.81\| 9.04\|
2.16\| 16.24\| 35.09\| 14.39\| 3.51\| 9.71\|

Conjunto 3

\|nombre \| llanura\| depresión/sima\| pico\| cresta (interfluvio no
inclinado)\| hombrera\| gajo (interfluvio inclinado)\| vertiente\|
vaguada\| piedemonte\| valle\|
\|:——————\|——-:\|————–:\|—-:\|——————————–:\|——–:\|—————————:\|———:\|——-:\|———-:\|—–:\|
\|DAJABÓN \| 12.17\| 0.39\| 1.02\| 9.63\| 3.83\| 14.02\| 30.65\| 12.87\|
4.98\| 10.44\| \|HERMANAS MIRABAL \| 21.30\| 0.78\| 1.36\| 9.58\| 4.72\|
11.48\| 24.53\| 10.84\| 4.28\| 11.12\| \|SAN JUAN \| 8.78\| 0.28\|
0.81\| 9.04\| 2.16\| 16.24\| 35.09\| 14.39\| 3.51\| 9.71\| \|SANTIAGO \|
5.82\| 0.60\| 1.14\| 11.25\| 1.60\| 17.25\| 32.15\| 15.95\| 2.08\|
12.18\| \|SANTIAGO RODRÍGUEZ \| 1.99\| 0.76\| 1.33\| 13.25\| 2.62\|
16.71\| 31.13\| 15.37\| 2.66\| 14.19\| \|VALVERDE \| 27.87\| 0.16\|
0.57\| 6.40\| 5.58\| 9.62\| 27.76\| 8.16\| 7.62\| 6.25\|

Conjunto 4

\|nombre \| llanura\| depresión/sima\| pico\| cresta (interfluvio no
inclinado)\| hombrera\| gajo (interfluvio inclinado)\| vertiente\|
vaguada\| piedemonte\| valle\|
\|:—————-\|——-:\|————–:\|—-:\|——————————–:\|——–:\|—————————:\|———:\|——-:\|———-:\|—–:\|
\|BAORUCO \| 28.09\| 0.18\| 0.61\| 6.66\| 1.54\| 12.39\| 29.65\| 10.87\|
2.60\| 7.41\| \|ESPAILLAT \| 11.24\| 0.98\| 1.70\| 12.00\| 3.68\|
13.89\| 27.07\| 12.86\| 3.23\| 13.35\| \|SANTIAGO \| 5.82\| 0.60\|
1.14\| 11.25\| 1.60\| 17.25\| 32.15\| 15.95\| 2.08\| 12.18\| \|VALVERDE
\| 27.87\| 0.16\| 0.57\| 6.40\| 5.58\| 9.62\| 27.76\| 8.16\| 7.62\|
6.25\| \|MONSEÑOR NOUEL \| 10.32\| 0.40\| 0.82\| 10.14\| 1.45\| 17.09\|
31.41\| 15.01\| 2.82\| 10.52\| \|SAN JOSÉ DE OCOA \| 1.26\| 0.61\|
1.09\| 12.12\| 0.23\| 19.83\| 32.97\| 17.98\| 0.61\| 13.31\|

Conjunto 5

\|nombre \| llanura\| depresión/sima\| pico\| cresta (interfluvio no
inclinado)\| hombrera\| gajo (interfluvio inclinado)\| vertiente\|
vaguada\| piedemonte\| valle\|
\|:——————\|——-:\|————–:\|—-:\|——————————–:\|——–:\|—————————:\|———:\|——-:\|———-:\|—–:\|
\|ELÍAS PIÑA \| 0.81\| 0.56\| 1.12\| 11.71\| 1.11\| 17.89\| 36.06\|
16.26\| 1.77\| 12.71\| \|EL SEIBO \| 14.57\| 0.32\| 1.15\| 9.90\| 5.24\|
12.03\| 28.32\| 10.88\| 7.10\| 10.48\| \|ESPAILLAT \| 11.24\| 0.98\|
1.70\| 12.00\| 3.68\| 13.89\| 27.07\| 12.86\| 3.23\| 13.35\|
\|INDEPENDENCIA \| 20.53\| 0.26\| 0.63\| 6.41\| 1.46\| 13.12\| 37.19\|
10.55\| 3.16\| 6.69\| \|HERMANAS MIRABAL \| 21.30\| 0.78\| 1.36\| 9.58\|
4.72\| 11.48\| 24.53\| 10.84\| 4.28\| 11.12\| \|SANTIAGO RODRÍGUEZ \|
1.99\| 0.76\| 1.33\| 13.25\| 2.62\| 16.71\| 31.13\| 15.37\| 2.66\|
14.19\|

Conjunto 6

\|nombre \| llanura\| depresión/sima\| pico\| cresta (interfluvio no
inclinado)\| hombrera\| gajo (interfluvio inclinado)\| vertiente\|
vaguada\| piedemonte\| valle\|
\|:———————-\|——-:\|————–:\|—-:\|——————————–:\|——–:\|—————————:\|———:\|——-:\|———-:\|—–:\|
\|BARAHONA \| 11.83\| 0.27\| 0.78\| 8.48\| 1.15\| 16.47\| 36.09\|
13.39\| 2.02\| 9.52\| \|ELÍAS PIÑA \| 0.81\| 0.56\| 1.12\| 11.71\|
1.11\| 17.89\| 36.06\| 16.26\| 1.77\| 12.71\| \|LA ALTAGRACIA \| 55.35\|
0.16\| 0.39\| 3.99\| 5.75\| 4.91\| 14.90\| 4.23\| 5.78\| 4.53\| \|MARÍA
TRINIDAD SÁNCHEZ \| 32.33\| 0.45\| 0.83\| 7.86\| 4.40\| 9.28\| 23.75\|
7.90\| 5.06\| 8.12\| \|PERAVIA \| 16.39\| 0.26\| 1.03\| 9.36\| 3.61\|
13.79\| 28.75\| 12.33\| 4.89\| 9.59\| \|SAN CRISTÓBAL \| 5.57\| 0.60\|
1.15\| 12.12\| 1.90\| 16.60\| 31.10\| 15.15\| 3.14\| 12.68\|

Conjunto 7

\|nombre \| llanura\| depresión/sima\| pico\| cresta (interfluvio no
inclinado)\| hombrera\| gajo (interfluvio inclinado)\| vertiente\|
vaguada\| piedemonte\| valle\|
\|:————-\|——-:\|————–:\|—-:\|——————————–:\|——–:\|—————————:\|———:\|——-:\|———-:\|—–:\|
\|ELÍAS PIÑA \| 0.81\| 0.56\| 1.12\| 11.71\| 1.11\| 17.89\| 36.06\|
16.26\| 1.77\| 12.71\| \|ESPAILLAT \| 11.24\| 0.98\| 1.70\| 12.00\|
3.68\| 13.89\| 27.07\| 12.86\| 3.23\| 13.35\| \|INDEPENDENCIA \| 20.53\|
0.26\| 0.63\| 6.41\| 1.46\| 13.12\| 37.19\| 10.55\| 3.16\| 6.69\| \|LA
ROMANA \| 57.78\| 0.29\| 0.11\| 3.23\| 9.67\| 2.92\| 11.74\| 2.70\|
7.20\| 4.37\| \|LA VEGA \| 14.44\| 0.49\| 1.03\| 10.82\| 1.07\| 16.03\|
28.01\| 14.83\| 1.41\| 11.87\| \|MONTE CRISTI \| 43.73\| 0.07\| 0.54\|
5.62\| 4.67\| 7.16\| 20.45\| 6.02\| 6.81\| 4.93\|

Conjunto 8

\|nombre \| llanura\| depresión/sima\| pico\| cresta (interfluvio no
inclinado)\| hombrera\| gajo (interfluvio inclinado)\| vertiente\|
vaguada\| piedemonte\| valle\|
\|:——————–\|——-:\|————–:\|—-:\|——————————–:\|——–:\|—————————:\|———:\|——-:\|———-:\|—–:\|
\|ESPAILLAT \| 11.24\| 0.98\| 1.70\| 12.00\| 3.68\| 13.89\| 27.07\|
12.86\| 3.23\| 13.35\| \|INDEPENDENCIA \| 20.53\| 0.26\| 0.63\| 6.41\|
1.46\| 13.12\| 37.19\| 10.55\| 3.16\| 6.69\| \|SAN CRISTÓBAL \| 5.57\|
0.60\| 1.15\| 12.12\| 1.90\| 16.60\| 31.10\| 15.15\| 3.14\| 12.68\|
\|SAN PEDRO DE MACORÍS \| 68.37\| 0.14\| 0.08\| 2.59\| 7.42\| 2.11\|
8.28\| 1.83\| 5.87\| 3.30\| \|VALVERDE \| 27.87\| 0.16\| 0.57\| 6.40\|
5.58\| 9.62\| 27.76\| 8.16\| 7.62\| 6.25\| \|MONSEÑOR NOUEL \| 10.32\|
0.40\| 0.82\| 10.14\| 1.45\| 17.09\| 31.41\| 15.01\| 2.82\| 10.52\|

Conjunto 9

\|nombre \| llanura\| depresión/sima\| pico\| cresta (interfluvio no
inclinado)\| hombrera\| gajo (interfluvio inclinado)\| vertiente\|
vaguada\| piedemonte\| valle\|
\|:————-\|——-:\|————–:\|—-:\|——————————–:\|——–:\|—————————:\|———:\|——-:\|———-:\|—–:\|
\|AZUA \| 8.54\| 0.28\| 0.98\| 9.75\| 1.55\| 16.64\| 34.34\| 14.79\|
2.73\| 10.41\| \|DUARTE \| 43.61\| 0.80\| 1.20\| 7.60\| 2.49\| 8.69\|
16.51\| 7.83\| 2.90\| 8.38\| \|ELÍAS PIÑA \| 0.81\| 0.56\| 1.12\|
11.71\| 1.11\| 17.89\| 36.06\| 16.26\| 1.77\| 12.71\| \|SAN CRISTÓBAL \|
5.57\| 0.60\| 1.15\| 12.12\| 1.90\| 16.60\| 31.10\| 15.15\| 3.14\|
12.68\| \|SANTIAGO \| 5.82\| 0.60\| 1.14\| 11.25\| 1.60\| 17.25\|
32.15\| 15.95\| 2.08\| 12.18\| \|VALVERDE \| 27.87\| 0.16\| 0.57\|
6.40\| 5.58\| 9.62\| 27.76\| 8.16\| 7.62\| 6.25\|

Conjunto 10

\|nombre \| llanura\| depresión/sima\| pico\| cresta (interfluvio no
inclinado)\| hombrera\| gajo (interfluvio inclinado)\| vertiente\|
vaguada\| piedemonte\| valle\|
\|:————\|——-:\|————–:\|—-:\|——————————–:\|——–:\|—————————:\|———:\|——-:\|———-:\|—–:\|
\|DAJABÓN \| 12.17\| 0.39\| 1.02\| 9.63\| 3.83\| 14.02\| 30.65\| 12.87\|
4.98\| 10.44\| \|EL SEIBO \| 14.57\| 0.32\| 1.15\| 9.90\| 5.24\| 12.03\|
28.32\| 10.88\| 7.10\| 10.48\| \|LA ROMANA \| 57.78\| 0.29\| 0.11\|
3.23\| 9.67\| 2.92\| 11.74\| 2.70\| 7.20\| 4.37\| \|LA VEGA \| 14.44\|
0.49\| 1.03\| 10.82\| 1.07\| 16.03\| 28.01\| 14.83\| 1.41\| 11.87\|
\|PUERTO PLATA \| 9.65\| 0.39\| 1.29\| 10.45\| 1.42\| 14.86\| 34.59\|
13.40\| 3.30\| 10.65\| \|MONTE PLATA \| 16.44\| 1.51\| 1.76\| 11.38\|
6.24\| 11.15\| 23.44\| 9.95\| 5.52\| 12.61\|

``` r
# sapply(conjuntos_l,
#        knitr::kable,
#        digits = 2,
#        format = ifelse(grepl('gfm', output_format), 'markdown', 'html'),
#        simplify = F)
```

4.  **Generación de la matriz de distancias de cada uno de los diez
    conjuntos**. Esta es la matriz de distancias con la que podrás
    realizar el agrupamiento jerárquico UPGMA, y podrás comprobar tus
    cálculos de distancias seleccionados.

``` r
# Mostrar la tabla generada
# sapply(conjuntos_par, knitr::kable(conjuntos_par))
# knitr::kable(data)
```

## **Mandato**.

> Ten presente un aspecto importante. El Tali te provee la matriz de
> distancias entre los 15 pares posibles para que realices el
> agrupamiento. Sin embargo, el Tali también te pide, en el punto 1.
> que, a modo de prueba, calcules la distancia euclidiana para dos pares
> de provincias elegidos al azar de entre los 15 posibles. Más detalles
> abajo.

1.  Usando los datos generados a partir de Martínez-Batlle (2022), para
    el conjunto que te tocó, obtén la distancia euclidiana entre dos
    pares de provincias elegidos por ti al azar (par A y par B), de los
    15 posibles que tiene tu matriz de distancias. Haz este cálculo
    usando todas las dimensiones disponibles (diez en total). Ten
    presente que, para incluir las diez dimensiones, deberás usar la
    fórmula provista, la cual considera un espacio n-dimensional. Es
    recomendable que, para los cálculos, redondees a dos cifras
    significativas. Esto tiene doble propósito: a) Comprobar la
    precisión del algoritmo distancias; b) Medir tu rendimiento
    calculando distancias en un n-dimensional.

2.  Aplica el método de agrupamiento jerárquico aglomerativo promedio no
    ponderado (UPGMA) para agrupar las provincias según sus formas
    predominantes, usando la matriz de distancias provista.

3.  Redacta, en un máximo de cuatro párrafos, lo siguiente:

- Introducción, en el que podrías incluir importancia del ejercicio,
  objetivo, justificación.
- Materiales y métodos, donde resumas que materiales usaste (esto
  incluye hasta el teléfono móvil, papel, lápiz, etc.), y las técnicas
  específicas empleadas, que en tu caso son la distancia y el método de
  agrupamiento enseñado.
- Resultado, lo cual supone describir, fríamente, lo que obtuviste.
- Discusión, donde indiques si alcanzaste el objetivo, interpretes el
  resultado, indiques las limitaciones y los posibles trabajos futuros.

# Ejemplo práctico

# ¿Cómo se haría el ejemplo práctico en R?

**Nota**: los cálculos se pueden realizar a mano o con una calculadora
del teléfono, utilizando las fórmulas proporcionadas.

## Referencias

<div id="refs" class="references csl-bib-body hanging-indent"
entry-spacing="0">

<div id="ref-borcard_numerical_2018" class="csl-entry">

Borcard, Daniel, François Gillet, and Pierre Legendre. 2018. *Numerical
Ecology with R*. Use R! Cham: Springer International Publishing.
<https://doi.org/10.1007/978-3-319-71404-2>.

</div>

<div id="ref-hengl2008geomorphometry" class="csl-entry">

Hengl, Tomislav, and Hannes I Reuter. 2008. *Geomorphometry: Concepts,
Software, Applications*. Vol. 33. Elsevier.

</div>

<div id="ref-jasiewicz2013geomorphons" class="csl-entry">

Jasiewicz, Jarosław, and Tomasz F Stepinski. 2013. “Geomorphons—a
Pattern Recognition Approach to Classification and Mapping of
Landforms.” *Geomorphology* 182: 147–56.

</div>

<div id="ref-jose_ramon_martinez_batlle_2022_7367180" class="csl-entry">

Martínez-Batlle, José Ramón. 2022.
“<span class="nocase">geofis/zonal-statistics: Let there be
environmental variables</span>.” Zenodo.
<https://doi.org/10.5281/zenodo.7367256>.

</div>

<div id="ref-triola2012estadistica" class="csl-entry">

Triola, M. F. 2012. *Estadistica*. Pearson Education.

</div>

</div>
