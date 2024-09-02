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

<!-- Qué son los DEM, cómo se obtienen, y qué importancia tienen (puedes citar a @hengl2008geomorphometry). -->
<!-- Qué son las técnicas de clasificación de formas del terreno (e.g. los geomórfonos), que se basan en DEM, citando a @hengl2008geomorphometry, @jasiewicz2013geomorphons -->
<!-- Qué son las técnicas de análisis de agrupamiento (UPGMA, etc), qué utilidad tienen @borcard_numerical_2018, -->
<!-- Una vez clasificados los elementos, cómo estas clasificaciones podrían ayudar a caracterizar más fácilmente y con técnicas estadísticas, unidades convencionales o físicas (@triola2012estadistica parte estadística). -->
<!-- Utilidad de la clasificación del relieve, qué problema se resuelve con ellos. -->
<!-- Cómo se pueden agrupar unidades como provincias, regiones o municipios, en función de la distribución porcentual de formas del terreno usando UPGMA, usando los datos de @jose_ramon_martinez_batlle_2022_7367180. -->
<!-- Qué aportes dan para el conjunto de la ciencia. -->

### Introducción

Los Modelos Digitales de Elevación (DEM, por sus siglas en inglés) son
representaciones digitales del relieve terrestre, obtenidas a partir de
diversos métodos, como el levantamiento topográfico, la teledetección o
el uso de sistemas de información geográfica (SIG) que permiten modelar
la superficie del terreno en tres dimensiones. Los DEM son fundamentales
en geomorfología y otras ciencias de la Tierra, ya que facilitan el
análisis de formas del relieve y ayudan a identificar patrones
geomorfológicos mediante técnicas cuantitativas (Hengl and Reuter 2008).

Las técnicas de clasificación de formas del terreno, como los
geomórfonos, se basan en la información proporcionada por los DEM para
identificar y categorizar características del relieve. Estas técnicas
permiten diferenciar unidades geomorfológicas y facilitan la comprensión
de la distribución espacial de las formas del terreno, proporcionando
una base sólida para análisis más detallados en estudios geográficos y
geológicos (Hengl and Reuter 2008; Jasiewicz and Stepinski 2013).

El análisis de agrupamiento, como el método de agrupamiento jerárquico
aglomerativo promedio no ponderado (UPGMA), se utiliza para clasificar
elementos en grupos homogéneos en función de sus características,
facilitando la identificación de patrones y relaciones entre diferentes
unidades territoriales. Este método es útil en diversos campos, desde la
biología hasta la geografía, para agrupar elementos con características
similares (Borcard, Gillet, and Legendre 2018).

Una vez clasificadas las unidades geomorfológicas, como provincias o
regiones, estas clasificaciones pueden facilitar su caracterización
mediante técnicas estadísticas, simplificando la comparación y el
análisis de unidades convencionales o físicas (Triola 2012). La utilidad
de estas clasificaciones radica en que permiten resolver problemas como
la identificación de zonas vulnerables a desastres naturales, la
planificación del uso del suelo y la conservación ambiental, entre
otros. Por ejemplo, las provincias de la República Dominicana pueden
agruparse según la distribución porcentual de formas del terreno
utilizando el método UPGMA, lo que proporciona una perspectiva
científica valiosa para diversos estudios geográficos y ambientales
(Martínez-Batlle 2022).

En resumen, las técnicas de clasificación y agrupamiento de formas del
terreno no solo contribuyen a la ciencia al facilitar la comprensión del
paisaje, sino que también aportan herramientas prácticas para la gestión
del territorio y la planificación ambiental.

# Ejercicio. Clasificar 6 provincias dominicanas según sus formas predominantes por el método de agrupamiento jerárquico aglomerativo promedio no ponderado (UPGMA)

## Objetivo

Agrupar las provincias de la República Dominicana según sus formas
predominantes del terreno utilizando el método de agrupamiento
jerárquico aglomerativo promedio no ponderado (UPGMA), con el fin de
identificar patrones geomorfológicos comunes y diferenciar unidades
territoriales en función de su distribución porcentual de formas del
terreno.

## Planteamiento del Problema

Se dispone de un conjunto de datos de Martínez-Batlle (2022), que
describe la distribución porcentual de las formas del terreno en
diferentes provincias de la República Dominicana, basado en un modelo
digital de elevación (DEM) y clasificaciones de formas del terreno
usando geomórfonos (Jasiewicz and Stepinski 2013). Se busca utilizar
estos datos para realizar un agrupamiento de las provincias según sus
características geomorfológicas predominantes, aplicando el método de
agrupamiento jerárquico UPGMA. Este agrupamiento permitirá identificar
similitudes y diferencias entre las provincias, facilitando el análisis
y la comprensión de los patrones del relieve a nivel regional.

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
conjuntos_l_k <- lapply(
  conjuntos_l,
  function(x) {
    colnames(x) <- gsub('\\n', '', colnames(x))
    knitr::kable(x, digits = 2, align = 'c')
    })
```

``` r
# Imprimir tablas
for (tabla in 1:length(conjuntos_l_k)) {
  cat('Conjunto', tabla, "\n\n")
  print(conjuntos_l_k[[tabla]])
  cat("\n\n")
}
```

Conjunto 1

|         nombre         | llanura | depresión/sima | pico | cresta(interfluvio no inclinado) | hombrera | gajo(interfluvio inclinado) | vertiente | vaguada | piedemonte | valle |
|:----------------------:|:-------:|:--------------:|:----:|:--------------------------------:|:--------:|:---------------------------:|:---------:|:-------:|:----------:|:-----:|
|        BAORUCO         |  28.09  |      0.18      | 0.61 |               6.66               |   1.54   |            12.39            |   29.65   |  10.87  |    2.60    | 7.41  |
|     INDEPENDENCIA      |  20.53  |      0.26      | 0.63 |               6.41               |   1.46   |            13.12            |   37.19   |  10.55  |    3.16    | 6.69  |
| MARÍA TRINIDAD SÁNCHEZ |  32.33  |      0.45      | 0.83 |               7.86               |   4.40   |            9.28             |   23.75   |  7.90   |    5.06    | 8.12  |
|      MONTE CRISTI      |  43.73  |      0.07      | 0.54 |               5.62               |   4.67   |            7.16             |   20.45   |  6.02   |    6.81    | 4.93  |
|    HERMANAS MIRABAL    |  21.30  |      0.78      | 1.36 |               9.58               |   4.72   |            11.48            |   24.53   |  10.84  |    4.28    | 11.12 |
|    SAN JOSÉ DE OCOA    |  1.26   |      0.61      | 1.09 |              12.12               |   0.23   |            19.83            |   32.97   |  17.98  |    0.61    | 13.31 |

Conjunto 2

|         nombre         | llanura | depresión/sima | pico | cresta(interfluvio no inclinado) | hombrera | gajo(interfluvio inclinado) | vertiente | vaguada | piedemonte | valle |
|:----------------------:|:-------:|:--------------:|:----:|:--------------------------------:|:--------:|:---------------------------:|:---------:|:-------:|:----------:|:-----:|
|        DAJABÓN         |  12.17  |      0.39      | 1.02 |               9.63               |   3.83   |            14.02            |   30.65   |  12.87  |    4.98    | 10.44 |
|     LA ALTAGRACIA      |  55.35  |      0.16      | 0.39 |               3.99               |   5.75   |            4.91             |   14.90   |  4.23   |    5.78    | 4.53  |
| MARÍA TRINIDAD SÁNCHEZ |  32.33  |      0.45      | 0.83 |               7.86               |   4.40   |            9.28             |   23.75   |  7.90   |    5.06    | 8.12  |
|      PUERTO PLATA      |  9.65   |      0.39      | 1.29 |              10.45               |   1.42   |            14.86            |   34.59   |  13.40  |    3.30    | 10.65 |
|         SAMANÁ         |  15.63  |      1.61      | 2.20 |              11.57               |   1.83   |            13.60            |   27.97   |  11.74  |    2.76    | 11.09 |
|        SAN JUAN        |  8.78   |      0.28      | 0.81 |               9.04               |   2.16   |            16.24            |   35.09   |  14.39  |    3.51    | 9.71  |

Conjunto 3

|       nombre       | llanura | depresión/sima | pico | cresta(interfluvio no inclinado) | hombrera | gajo(interfluvio inclinado) | vertiente | vaguada | piedemonte | valle |
|:------------------:|:-------:|:--------------:|:----:|:--------------------------------:|:--------:|:---------------------------:|:---------:|:-------:|:----------:|:-----:|
|      DAJABÓN       |  12.17  |      0.39      | 1.02 |               9.63               |   3.83   |            14.02            |   30.65   |  12.87  |    4.98    | 10.44 |
|  HERMANAS MIRABAL  |  21.30  |      0.78      | 1.36 |               9.58               |   4.72   |            11.48            |   24.53   |  10.84  |    4.28    | 11.12 |
|      SAN JUAN      |  8.78   |      0.28      | 0.81 |               9.04               |   2.16   |            16.24            |   35.09   |  14.39  |    3.51    | 9.71  |
|      SANTIAGO      |  5.82   |      0.60      | 1.14 |              11.25               |   1.60   |            17.25            |   32.15   |  15.95  |    2.08    | 12.18 |
| SANTIAGO RODRÍGUEZ |  1.99   |      0.76      | 1.33 |              13.25               |   2.62   |            16.71            |   31.13   |  15.37  |    2.66    | 14.19 |
|      VALVERDE      |  27.87  |      0.16      | 0.57 |               6.40               |   5.58   |            9.62             |   27.76   |  8.16   |    7.62    | 6.25  |

Conjunto 4

|      nombre      | llanura | depresión/sima | pico | cresta(interfluvio no inclinado) | hombrera | gajo(interfluvio inclinado) | vertiente | vaguada | piedemonte | valle |
|:----------------:|:-------:|:--------------:|:----:|:--------------------------------:|:--------:|:---------------------------:|:---------:|:-------:|:----------:|:-----:|
|     BAORUCO      |  28.09  |      0.18      | 0.61 |               6.66               |   1.54   |            12.39            |   29.65   |  10.87  |    2.60    | 7.41  |
|    ESPAILLAT     |  11.24  |      0.98      | 1.70 |              12.00               |   3.68   |            13.89            |   27.07   |  12.86  |    3.23    | 13.35 |
|     SANTIAGO     |  5.82   |      0.60      | 1.14 |              11.25               |   1.60   |            17.25            |   32.15   |  15.95  |    2.08    | 12.18 |
|     VALVERDE     |  27.87  |      0.16      | 0.57 |               6.40               |   5.58   |            9.62             |   27.76   |  8.16   |    7.62    | 6.25  |
|  MONSEÑOR NOUEL  |  10.32  |      0.40      | 0.82 |              10.14               |   1.45   |            17.09            |   31.41   |  15.01  |    2.82    | 10.52 |
| SAN JOSÉ DE OCOA |  1.26   |      0.61      | 1.09 |              12.12               |   0.23   |            19.83            |   32.97   |  17.98  |    0.61    | 13.31 |

Conjunto 5

|       nombre       | llanura | depresión/sima | pico | cresta(interfluvio no inclinado) | hombrera | gajo(interfluvio inclinado) | vertiente | vaguada | piedemonte | valle |
|:------------------:|:-------:|:--------------:|:----:|:--------------------------------:|:--------:|:---------------------------:|:---------:|:-------:|:----------:|:-----:|
|     ELÍAS PIÑA     |  0.81   |      0.56      | 1.12 |              11.71               |   1.11   |            17.89            |   36.06   |  16.26  |    1.77    | 12.71 |
|      EL SEIBO      |  14.57  |      0.32      | 1.15 |               9.90               |   5.24   |            12.03            |   28.32   |  10.88  |    7.10    | 10.48 |
|     ESPAILLAT      |  11.24  |      0.98      | 1.70 |              12.00               |   3.68   |            13.89            |   27.07   |  12.86  |    3.23    | 13.35 |
|   INDEPENDENCIA    |  20.53  |      0.26      | 0.63 |               6.41               |   1.46   |            13.12            |   37.19   |  10.55  |    3.16    | 6.69  |
|  HERMANAS MIRABAL  |  21.30  |      0.78      | 1.36 |               9.58               |   4.72   |            11.48            |   24.53   |  10.84  |    4.28    | 11.12 |
| SANTIAGO RODRÍGUEZ |  1.99   |      0.76      | 1.33 |              13.25               |   2.62   |            16.71            |   31.13   |  15.37  |    2.66    | 14.19 |

Conjunto 6

|         nombre         | llanura | depresión/sima | pico | cresta(interfluvio no inclinado) | hombrera | gajo(interfluvio inclinado) | vertiente | vaguada | piedemonte | valle |
|:----------------------:|:-------:|:--------------:|:----:|:--------------------------------:|:--------:|:---------------------------:|:---------:|:-------:|:----------:|:-----:|
|        BARAHONA        |  11.83  |      0.27      | 0.78 |               8.48               |   1.15   |            16.47            |   36.09   |  13.39  |    2.02    | 9.52  |
|       ELÍAS PIÑA       |  0.81   |      0.56      | 1.12 |              11.71               |   1.11   |            17.89            |   36.06   |  16.26  |    1.77    | 12.71 |
|     LA ALTAGRACIA      |  55.35  |      0.16      | 0.39 |               3.99               |   5.75   |            4.91             |   14.90   |  4.23   |    5.78    | 4.53  |
| MARÍA TRINIDAD SÁNCHEZ |  32.33  |      0.45      | 0.83 |               7.86               |   4.40   |            9.28             |   23.75   |  7.90   |    5.06    | 8.12  |
|        PERAVIA         |  16.39  |      0.26      | 1.03 |               9.36               |   3.61   |            13.79            |   28.75   |  12.33  |    4.89    | 9.59  |
|     SAN CRISTÓBAL      |  5.57   |      0.60      | 1.15 |              12.12               |   1.90   |            16.60            |   31.10   |  15.15  |    3.14    | 12.68 |

Conjunto 7

|    nombre     | llanura | depresión/sima | pico | cresta(interfluvio no inclinado) | hombrera | gajo(interfluvio inclinado) | vertiente | vaguada | piedemonte | valle |
|:-------------:|:-------:|:--------------:|:----:|:--------------------------------:|:--------:|:---------------------------:|:---------:|:-------:|:----------:|:-----:|
|  ELÍAS PIÑA   |  0.81   |      0.56      | 1.12 |              11.71               |   1.11   |            17.89            |   36.06   |  16.26  |    1.77    | 12.71 |
|   ESPAILLAT   |  11.24  |      0.98      | 1.70 |              12.00               |   3.68   |            13.89            |   27.07   |  12.86  |    3.23    | 13.35 |
| INDEPENDENCIA |  20.53  |      0.26      | 0.63 |               6.41               |   1.46   |            13.12            |   37.19   |  10.55  |    3.16    | 6.69  |
|   LA ROMANA   |  57.78  |      0.29      | 0.11 |               3.23               |   9.67   |            2.92             |   11.74   |  2.70   |    7.20    | 4.37  |
|    LA VEGA    |  14.44  |      0.49      | 1.03 |              10.82               |   1.07   |            16.03            |   28.01   |  14.83  |    1.41    | 11.87 |
| MONTE CRISTI  |  43.73  |      0.07      | 0.54 |               5.62               |   4.67   |            7.16             |   20.45   |  6.02   |    6.81    | 4.93  |

Conjunto 8

|        nombre        | llanura | depresión/sima | pico | cresta(interfluvio no inclinado) | hombrera | gajo(interfluvio inclinado) | vertiente | vaguada | piedemonte | valle |
|:--------------------:|:-------:|:--------------:|:----:|:--------------------------------:|:--------:|:---------------------------:|:---------:|:-------:|:----------:|:-----:|
|      ESPAILLAT       |  11.24  |      0.98      | 1.70 |              12.00               |   3.68   |            13.89            |   27.07   |  12.86  |    3.23    | 13.35 |
|    INDEPENDENCIA     |  20.53  |      0.26      | 0.63 |               6.41               |   1.46   |            13.12            |   37.19   |  10.55  |    3.16    | 6.69  |
|    SAN CRISTÓBAL     |  5.57   |      0.60      | 1.15 |              12.12               |   1.90   |            16.60            |   31.10   |  15.15  |    3.14    | 12.68 |
| SAN PEDRO DE MACORÍS |  68.37  |      0.14      | 0.08 |               2.59               |   7.42   |            2.11             |   8.28    |  1.83   |    5.87    | 3.30  |
|       VALVERDE       |  27.87  |      0.16      | 0.57 |               6.40               |   5.58   |            9.62             |   27.76   |  8.16   |    7.62    | 6.25  |
|    MONSEÑOR NOUEL    |  10.32  |      0.40      | 0.82 |              10.14               |   1.45   |            17.09            |   31.41   |  15.01  |    2.82    | 10.52 |

Conjunto 9

|    nombre     | llanura | depresión/sima | pico | cresta(interfluvio no inclinado) | hombrera | gajo(interfluvio inclinado) | vertiente | vaguada | piedemonte | valle |
|:-------------:|:-------:|:--------------:|:----:|:--------------------------------:|:--------:|:---------------------------:|:---------:|:-------:|:----------:|:-----:|
|     AZUA      |  8.54   |      0.28      | 0.98 |               9.75               |   1.55   |            16.64            |   34.34   |  14.79  |    2.73    | 10.41 |
|    DUARTE     |  43.61  |      0.80      | 1.20 |               7.60               |   2.49   |            8.69             |   16.51   |  7.83   |    2.90    | 8.38  |
|  ELÍAS PIÑA   |  0.81   |      0.56      | 1.12 |              11.71               |   1.11   |            17.89            |   36.06   |  16.26  |    1.77    | 12.71 |
| SAN CRISTÓBAL |  5.57   |      0.60      | 1.15 |              12.12               |   1.90   |            16.60            |   31.10   |  15.15  |    3.14    | 12.68 |
|   SANTIAGO    |  5.82   |      0.60      | 1.14 |              11.25               |   1.60   |            17.25            |   32.15   |  15.95  |    2.08    | 12.18 |
|   VALVERDE    |  27.87  |      0.16      | 0.57 |               6.40               |   5.58   |            9.62             |   27.76   |  8.16   |    7.62    | 6.25  |

Conjunto 10

|    nombre    | llanura | depresión/sima | pico | cresta(interfluvio no inclinado) | hombrera | gajo(interfluvio inclinado) | vertiente | vaguada | piedemonte | valle |
|:------------:|:-------:|:--------------:|:----:|:--------------------------------:|:--------:|:---------------------------:|:---------:|:-------:|:----------:|:-----:|
|   DAJABÓN    |  12.17  |      0.39      | 1.02 |               9.63               |   3.83   |            14.02            |   30.65   |  12.87  |    4.98    | 10.44 |
|   EL SEIBO   |  14.57  |      0.32      | 1.15 |               9.90               |   5.24   |            12.03            |   28.32   |  10.88  |    7.10    | 10.48 |
|  LA ROMANA   |  57.78  |      0.29      | 0.11 |               3.23               |   9.67   |            2.92             |   11.74   |  2.70   |    7.20    | 4.37  |
|   LA VEGA    |  14.44  |      0.49      | 1.03 |              10.82               |   1.07   |            16.03            |   28.01   |  14.83  |    1.41    | 11.87 |
| PUERTO PLATA |  9.65   |      0.39      | 1.29 |              10.45               |   1.42   |            14.86            |   34.59   |  13.40  |    3.30    | 10.65 |
| MONTE PLATA  |  16.44  |      1.51      | 1.76 |              11.38               |   6.24   |            11.15            |   23.44   |  9.95   |    5.52    | 12.61 |

4.  **Generación de la matriz de distancias de cada uno de los diez
    conjuntos**. Esta es la matriz de distancias con la que podrás
    realizar el agrupamiento jerárquico UPGMA, y podrás comprobar tus
    cálculos de distancias seleccionados.

``` r
# Mostrar las matrices de distancia
print(sapply(
  conjuntos_l,
  function(x)
    x %>% as.data.frame() %>% column_to_rownames('nombre') %>% dist,
  simplify = F),
  digits = 2)
```

    ## $`Conjunto 1`
    ##                        BAORUCO INDEPENDENCIA MARÍA TRINIDAD SÁNCHEZ
    ## INDEPENDENCIA             10.7                                     
    ## MARÍA TRINIDAD SÁNCHEZ     9.4          18.9                       
    ## MONTE CRISTI              20.4          30.0                   12.9
    ## HERMANAS MIRABAL          10.5          14.4                   12.2
    ## SAN JOSÉ DE OCOA          30.1          24.0                   36.7
    ##                        MONTE CRISTI HERMANAS MIRABAL
    ## INDEPENDENCIA                                       
    ## MARÍA TRINIDAD SÁNCHEZ                              
    ## MONTE CRISTI                                        
    ## HERMANAS MIRABAL               25.0                 
    ## SAN JOSÉ DE OCOA               49.3             25.3
    ## 
    ## $`Conjunto 2`
    ##                        DAJABÓN LA ALTAGRACIA MARÍA TRINIDAD SÁNCHEZ
    ## LA ALTAGRACIA             48.4                                     
    ## MARÍA TRINIDAD SÁNCHEZ    22.6          25.9                       
    ## PUERTO PLATA               5.7          52.6                   26.8
    ## SAMANÁ                     6.1          44.8                   19.2
    ## SAN JUAN                   6.7          53.7                   28.0
    ##                        PUERTO PLATA SAMANÁ
    ## LA ALTAGRACIA                             
    ## MARÍA TRINIDAD SÁNCHEZ                    
    ## PUERTO PLATA                              
    ## SAMANÁ                          9.4       
    ## SAN JUAN                        2.8   11.1
    ## 
    ## $`Conjunto 3`
    ##                    DAJABÓN HERMANAS MIRABAL SAN JUAN SANTIAGO
    ## HERMANAS MIRABAL      11.6                                   
    ## SAN JUAN               6.7             17.7                  
    ## SANTIAGO               9.0             19.4      5.9         
    ## SANTIAGO RODRÍGUEZ    12.3             22.2     10.1      5.1
    ## VALVERDE              18.3             10.5     23.4     27.0
    ##                    SANTIAGO RODRÍGUEZ
    ## HERMANAS MIRABAL                     
    ## SAN JUAN                             
    ## SANTIAGO                             
    ## SANTIAGO RODRÍGUEZ                   
    ## VALVERDE                         30.5
    ## 
    ## $`Conjunto 4`
    ##                  BAORUCO ESPAILLAT SANTIAGO VALVERDE MONSEÑOR NOUEL
    ## ESPAILLAT           19.2                                           
    ## SANTIAGO            24.4       9.2                                 
    ## VALVERDE             7.8      20.6     27.0                        
    ## MONSEÑOR NOUEL      19.5       7.2      5.1     22.3               
    ## SAN JOSÉ DE OCOA    30.1      14.7      6.2     33.1           10.9
    ## 
    ## $`Conjunto 5`
    ##                    ELÍAS PIÑA EL SEIBO ESPAILLAT INDEPENDENCIA HERMANAS MIRABAL
    ## EL SEIBO                 19.1                                                  
    ## ESPAILLAT                15.1      7.1                                         
    ## INDEPENDENCIA            22.6     13.1      16.6                               
    ## HERMANAS MIRABAL         25.5      8.3      11.4          14.4                 
    ## SANTIAGO RODRÍGUEZ        6.0     16.1      11.0          22.8             22.2
    ## 
    ## $`Conjunto 6`
    ##                        BARAHONA ELÍAS PIÑA LA ALTAGRACIA MARÍA TRINIDAD SÁNCHEZ
    ## ELÍAS PIÑA                 12.4                                                
    ## LA ALTAGRACIA              51.4       62.5                                     
    ## MARÍA TRINIDAD SÁNCHEZ     26.0       36.7          25.9                       
    ## PERAVIA                     9.9       19.0          43.8                   18.0
    ## SAN CRISTÓBAL               9.6        7.3          56.1                   30.4
    ##                        PERAVIA
    ## ELÍAS PIÑA                    
    ## LA ALTAGRACIA                 
    ## MARÍA TRINIDAD SÁNCHEZ        
    ## PERAVIA                       
    ## SAN CRISTÓBAL             12.7
    ## 
    ## $`Conjunto 7`
    ##               ELÍAS PIÑA ESPAILLAT INDEPENDENCIA LA ROMANA LA VEGA
    ## ESPAILLAT           15.1                                          
    ## INDEPENDENCIA       22.6      16.6                                
    ## LA ROMANA           67.0      53.3          48.0                  
    ## LA VEGA             16.1       5.8          14.1      51.8        
    ## MONTE CRISTI        49.4      36.3          30.0      18.3    34.5
    ## 
    ## $`Conjunto 8`
    ##                      ESPAILLAT INDEPENDENCIA SAN CRISTÓBAL SAN PEDRO DE MACORÍS
    ## INDEPENDENCIA             16.6                                                 
    ## SAN CRISTÓBAL              8.1          19.1                                   
    ## SAN PEDRO DE MACORÍS      64.0          58.2          71.2                     
    ## VALVERDE                  20.6          14.1          26.7                 46.3
    ## MONSEÑOR NOUEL             7.2          14.2           5.7                 66.8
    ##                      VALVERDE
    ## INDEPENDENCIA                
    ## SAN CRISTÓBAL                
    ## SAN PEDRO DE MACORÍS         
    ## VALVERDE                     
    ## MONSEÑOR NOUEL           22.3
    ## 
    ## $`Conjunto 9`
    ##               AZUA DUARTE ELÍAS PIÑA SAN CRISTÓBAL SANTIAGO
    ## DUARTE        40.9                                         
    ## ELÍAS PIÑA     8.8   49.1                                  
    ## SAN CRISTÓBAL  5.5   42.6        7.3                       
    ## SANTIAGO       4.5   42.9        6.5           2.1         
    ## VALVERDE      24.1   20.3       32.6          26.7     27.0
    ## 
    ## $`Conjunto 10`
    ##              DAJABÓN EL SEIBO LA ROMANA LA VEGA PUERTO PLATA
    ## EL SEIBO         5.1                                        
    ## LA ROMANA       52.7     48.9                               
    ## LA VEGA          6.6      9.2      51.8                     
    ## PUERTO PLATA     5.7     10.4      57.2     8.7             
    ## MONTE PLATA     10.1      6.4      46.0    10.9         15.2

## **Mandato**.

> Ten presente un aspecto importante. El Tali te provee la matriz de
> distancias entre los 15 pares posibles para que realices el
> agrupamiento. Sin embargo, el Tali también te pide, en el punto 1.
> que, a modo de prueba, calcules la distancia euclidiana para dos pares
> de provincias elegidos al azar de entre los 15 posibles. Más detalles
> abajo.

1.  Usando los datos generados a partir de Martínez-Batlle (2022), para
    el conjunto que te tocó, obtén la distancia euclidiana entre un par
    de provincias elegidos por ti al azar; es decir, un par de entre los
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

En el aula.

1.  Calcular distancia entre dos provincias.

Para calcular la distancia entre dos provincias cualquiera utilizando
las diez dimensiones proporcionadas (las columnas de distribución
porcentual de las formas del terreno), se puede utilizar la **distancia
euclidiana** en un espacio n-dimensional. La fórmula para calcular la
distancia euclidiana entre dos puntos $A$ y $B$ en un espacio de $n$
dimensiones es:

$$
d(A, B) = \sqrt{\sum_{i=1}^{n} (x_{i} - y_{i})^2}
$$

donde: - $d(A, B)$ es la distancia euclidiana entre los puntos $A$ y
$B$. - $x_{i}$ y $y_{i}$ son las coordenadas (en este caso, los
porcentajes de cada forma del terreno) de los puntos $A$ y $B$ en la
dimensión $i$. - $n = 10$, que corresponde al número de dimensiones o
variables consideradas.

- Ejemplo de cálculo:

Vamos a calcular la distancia euclidiana entre las provincias
**BAORUCO** e **INDEPENDENCIA**:

| Proporción de forma del terreno   | BAORUCO ($x_i$) | INDEPENDENCIA ($y_i$) |               $(x_i - y_i)^2$ |
|:----------------------------------|----------------:|----------------------:|------------------------------:|
| Llanura                           |           28.09 |                 20.53 |   $(28.09 - 20.53)^2 = 57.35$ |
| Depresión/sima                    |            0.18 |                  0.26 |    $(0.18 - 0.26)^2 = 0.0064$ |
| Pico                              |            0.61 |                  0.63 |    $(0.61 - 0.63)^2 = 0.0004$ |
| Cresta (interfluvio no inclinado) |            6.66 |                  6.41 |    $(6.66 - 6.41)^2 = 0.0625$ |
| Hombrera                          |            1.54 |                  1.46 |    $(1.54 - 1.46)^2 = 0.0064$ |
| Gajo (interfluvio inclinado)      |           12.39 |                 13.12 |  $(12.39 - 13.12)^2 = 0.5329$ |
| Vertiente                         |           29.65 |                 37.19 | $(29.65 - 37.19)^2 = 56.7121$ |
| Vaguada                           |           10.87 |                 10.55 |  $(10.87 - 10.55)^2 = 0.1024$ |
| Piedemonte                        |            2.60 |                  3.16 |    $(2.60 - 3.16)^2 = 0.3136$ |
| Valle                             |            7.41 |                  6.69 |    $(7.41 - 6.69)^2 = 0.5184$ |

Sumamos todas las diferencias al cuadrado:

$$
\sum_{i=1}^{10} (x_{i} - y_{i})^2 = 57.35 + 0.0064 + 0.0004 + 0.0625 + 0.0064 + 0.5329 + 56.7121 + 0.1024 + 0.3136 + 0.5184 = 115.61
$$

Luego, tomamos la raíz cuadrada de esta suma para obtener la distancia
euclidiana:

$$
d(\text{BAORUCO, INDEPENDENCIA}) = \sqrt{115.61} \approx 10.75
$$

- Resultado

La distancia euclidiana entre las provincias **BAORUCO** e
**INDEPENDENCIA** es aproximadamente **10.75** unidades porcentuales.
Esta distancia representa la magnitud de la diferencia en la
distribución de formas del terreno entre las dos provincias
consideradas.

2.  Aplicar el método UPGMA

Para realizar un agrupamiento jerárquico utilizando el método de
agrupamiento jerárquico aglomerativo promedio no ponderado (UPGMA),
seguimos un enfoque iterativo que consiste en agrupar los pares de
elementos o clusters más cercanos hasta que todos los elementos estén en
un único cluster. UPGMA utiliza las distancias promedio entre todos los
miembros de los clusters para calcular la distancia entre clusters.
[Este vídeo](https://www.youtube.com/watch?v=RdT7bhm1M3E) también podría
resultarte útil, aunque en el vídeo, la instructora se basa en el
vínculo simple, no en el promedio; es decir, ella, en lugar de obtener
promedios a la hora de recalcular la matriz de distancias, lo que
obtiene son valores mínimos, pero en nuestro casos serían promedios.

- Matriz de Distancias Inicial

Dado el conjunto de datos:

|                            | BAORUCO | INDEPENDENCIA | MARÍA TRINIDAD SÁNCHEZ | MONTE CRISTI | HERMANAS MIRABAL | SAN JOSÉ DE OCOA |
|----------------------------|---------|---------------|------------------------|--------------|------------------|------------------|
| **INDEPENDENCIA**          | 10.7    |               |                        |              |                  |                  |
| **MARÍA TRINIDAD SÁNCHEZ** | 9.4     | 18.9          |                        |              |                  |                  |
| **MONTE CRISTI**           | 20.4    | 30.0          | 12.9                   |              |                  |                  |
| **HERMANAS MIRABAL**       | 10.5    | 14.4          | 12.2                   | 25.0         |                  |                  |
| **SAN JOSÉ DE OCOA**       | 30.1    | 24.0          | 36.7                   | 49.3         | 25.3             |                  |

- Paso 1: Encuentra el par con la distancia más pequeña

- El par con la distancia más pequeña es **BAORUCO** y **MARÍA TRINIDAD
  SÁNCHEZ** con una distancia de 9.4.

- Paso 2: Agrupa los elementos más cercanos

- Agrupamos **BAORUCO** y **MARÍA TRINIDAD SÁNCHEZ** en un nuevo cluster
  (B-MTS).

- Calculamos la distancia entre este nuevo cluster (B-MTS) y los otros
  elementos utilizando el promedio de las distancias:

$$
\text{Distancia}(B-MTS, \text{INDEPENDENCIA}) = \frac{10.7 + 18.9}{2} = 14.8
$$

$$
\text{Distancia}(B-MTS, \text{MONTE CRISTI}) = \frac{20.4 + 12.9}{2} = 16.65
$$

$$
\text{Distancia}(B-MTS, \text{HERMANAS MIRABAL}) = \frac{10.5 + 12.2}{2} = 11.35
$$

$$
\text{Distancia}(B-MTS, \text{SAN JOSÉ DE OCOA}) = \frac{30.1 + 36.7}{2} = 33.4
$$

- Nueva matriz de distancias:

|                      | B-MTS | INDEPENDENCIA | MONTE CRISTI | HERMANAS MIRABAL | SAN JOSÉ DE OCOA |
|----------------------|-------|---------------|--------------|------------------|------------------|
| **B-MTS**            |       | 14.8          | 16.65        | 11.35            | 33.4             |
| **INDEPENDENCIA**    | 14.8  |               | 30.0         | 14.4             | 24.0             |
| **MONTE CRISTI**     | 16.65 | 30.0          |              | 25.0             | 49.3             |
| **HERMANAS MIRABAL** | 11.35 | 14.4          | 25.0         |                  | 25.3             |
| **SAN JOSÉ DE OCOA** | 33.4  | 24.0          | 49.3         | 25.3             |                  |

- Paso 3: Encuentra el Siguiente Par con la Distancia más Pequeña

- El par con la distancia más pequeña es **B-MTS** y **HERMANAS
  MIRABAL** con una distancia de 11.35.

- Paso 4: Agrupa el Nuevo Cluster

- Agrupamos **B-MTS** y **HERMANAS MIRABAL** en un nuevo cluster
  (B-MTS-HM).

- Calculamos la distancia entre el nuevo cluster (B-MTS-HM) y los otros
  elementos:

$$
\text{Distancia}(B-MTS-HM, \text{INDEPENDENCIA}) = \frac{14.8 + 14.4}{2} = 14.6
$$

$$
\text{Distancia}(B-MTS-HM, \text{MONTE CRISTI}) = \frac{16.65 + 25.0}{2} = 20.825
$$

$$
\text{Distancia}(B-MTS-HM, \text{SAN JOSÉ DE OCOA}) = \frac{33.4 + 25.3}{2} = 29.35
$$

- Nueva matriz de distancias:

|                      | B-MTS-HM | INDEPENDENCIA | MONTE CRISTI | SAN JOSÉ DE OCOA |
|----------------------|----------|---------------|--------------|------------------|
| **B-MTS-HM**         |          | 14.6          | 20.825       | 29.35            |
| **INDEPENDENCIA**    | 14.6     |               | 30.0         | 24.0             |
| **MONTE CRISTI**     | 20.825   | 30.0          |              | 49.3             |
| **SAN JOSÉ DE OCOA** | 29.35    | 24.0          | 49.3         |                  |

- Paso 5: Repite el Proceso Hasta Que Todos los Elementos Sean Agrupados

1.  Agrupa **B-MTS-HM** y **INDEPENDENCIA** (distancia de 14.6).

2.  Continúa agrupando siguiendo el mismo proceso iterativo hasta que
    todas las provincias estén en un solo cluster.

- Dendrograma

El resultado final de este proceso se puede representar mediante un
dendrograma, que muestra la estructura jerárquica de los agrupamientos:

               +--------- SAN JOSÉ DE OCOA
               |
               |       +--- MONTE CRISTI
               |-------+
               |       |
    +----------+       +--- (BAORUCO, MARÍA TRINIDAD SÁNCHEZ, HERMANAS MIRABAL)
    |
    +----------------- INDEPENDENCIA

- Conclusión

Este procedimiento de UPGMA nos permite agrupar las provincias en
función de sus similitudes geomorfológicas basadas en la distancia
euclidiana de sus características. Esto ayuda a identificar patrones de
distribución de formas del terreno y a realizar análisis comparativos
entre diferentes unidades territoriales.

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
