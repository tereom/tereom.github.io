# Tipos de estudio y experimentos



<!-- ```{block, type='ejercicio'} -->
<!--   **Pregunta de entrevista de Google [@Chihara]**   -->
<!--   Imagina que eres consultor y te preguntan lo siguiente (ver siguiente figura):   -->
<!--   Estoy haciendo una comparación de antes y después donde la hipótesis alternativa -->
<!--   es pre.media.error > post.media.error. La distribución de ambas muestras es -->
<!--   sesgada a la derecha. ¿Qué prueba me recomiendas para ésta situación? -->
<!-- ``` -->

<!-- ```{r grafica-pcr, echo=FALSE, warning=FALSE, message=FALSE, fig.cap = "Error CPR, gráfica de densidad.", fig.height = 3.2, fig.width = 4.2} -->
<!-- library(tidyverse) -->
<!-- theme_set(theme_minimal()) -->
<!-- pre <- tibble(group = "Pre", y = rgamma(10000, 2, 0.8)-0.2) -->
<!-- post <- tibble(group = "Post", y = rgamma(10000, 1.7, 0.8)-0.2) -->
<!-- ggplot(bind_rows(pre, post), aes(x = y, color = group)) + -->
<!--   geom_density() + -->
<!--   xlab("CPR error") + labs(color = "") -->
<!-- ``` -->


La siguiente imagen de [Roger Peng](https://simplystatistics.org/2019/04/17/tukey-design-thinking-and-better-questions/)
representa una situación común a la que se enfrenta el analista de datos, y se
desarrolló en el contexto de preguntas vagas. En el esquema hay tres caminos:
  uno es ideal que pocas veces sucede,
otro produce respuestas poco útiles pero es fácil, y otro es tortuoso pero que
caracteriza el mejor trabajo de análisis de datos:


<div class="figure">
<img src="03-tipos-de-estudio_files/figure-html/unnamed-chunk-1-1.png" alt="Adaptado de R. Peng: [Tukey, design thinking and better questions.](https://simplystatistics.org/2019/04/17/tukey-design-thinking-and-better-questions/)" width="672" />
<p class="caption">(\#fig:unnamed-chunk-1)Adaptado de R. Peng: [Tukey, design thinking and better questions.](https://simplystatistics.org/2019/04/17/tukey-design-thinking-and-better-questions/)</p>
</div>


## De datos y poblaciones {-}

Los datos no son el fin último de un estudio. Son el mecanismo que podemos utilizar para poder contestar
preguntas acerca de la población que no vemos. Pensemos en la encuesta realizada en el Reino Unido
sobre parejas sexuales del sexo opuesto
que una persona en el rango de edad de 35-44 años declara tener. Este estudio está tomado de @spiegelhalter2019art.
Los datos están reportados en la encuesta Natsal-3 que puede encontrarse en [C.H. Mercer et al., ‘Changes in Sexual Attitudes and Lifestyles in Britain through the Life Course and Over Time: Findings from the National Surveys of Sexual Attitudes and Lifestyles (Natsal)’, 2013](https://www.thelancet.com/journals/lancet/article/PIIS0140-6736(13)62035-8/fulltext).

Estos datos corresponden a un total de 796 hombres y 1,193 mujeres encuestadas, y están ponderados por el diseño
estratificado de la encuesta.


```
##    NumParejas ConteoH MenPercent ConteoM WomenPercent
## 1          38       2       0.25       0         0.00
## 2         201       0       0.00       0         0.00
## 3          74       0       0.00       0         0.00
## 4          11       9       1.13      20         1.68
## 5          56       0       0.00       0         0.00
## 6          63       0       0.00       1         0.08
## 7         303       0       0.00       0         0.00
## 8          43       0       0.00       0         0.00
## 9         350       0       0.00       0         0.00
## 10         26       4       0.50       2         0.17
```

Como en los ejemplos anteriores, podemos calcular un resumen rápido para hombres:


```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##    0.00    4.00    8.00   16.98   20.00  501.00
```

así como un resumen rápido para las mujeres encuestadas:


```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##    0.00    2.00    5.00    8.23   10.00  550.00
```

Un gráfico sencillo nos ayudará a ilustrar la distirbución de las respuestas por género:

```r
nastal %>%
  select(NumParejas, ConteoH, ConteoM) %>%
  rename(Hombres = ConteoH, Mujeres = ConteoM) %>%
  gather('Hombres', 'Mujeres', key='Género', value = 'Conteo') %>%
  ggplot(aes(x = NumParejas)) +
    geom_bar(aes(y = Conteo, fill = Género), stat = 'identity', position = 'dodge') +
    scale_x_continuous(breaks = c(0,5,10,15,20,25,30,35,40,45,50), limits=c(0,50)) +
    scale_colour_brewer(palette = "Set1") +
    labs(x="Número reportado de parejas sexuales del genero opuesto")
```

<img src="03-tipos-de-estudio_files/figure-html/unnamed-chunk-5-1.png" width="95%" style="display: block; margin: auto;" />
¿Cómo podemos generalizar a la población después de haber observado dichos resultados en la encuesta?

<div class="comentario">
<p>Podemos seguir el siguiente tren de pensamiento:</p>
<ul>
<li>El <em>número registrado</em> de parejas sexuales de los participantes nos habla acerca de …<br />
</li>
<li>El <em>número real</em> de parejas sexuales en nuestra <em>muestra</em>, lo que nos habla acerca de …<br />
</li>
<li>El número de parejas en la <em>población de estudio</em>, lo que nos habla acerca de …<br />
</li>
<li>El número de parejas sexuales en el Reino Unido, lo cual es la <em>población objetivo</em>.</li>
</ul>
</div>

<div class="comentario">
<p>Los puntos más débiles en la generalización son los siguientes:</p>
<ul>
<li>¿Podemos asumir que los encuestados responderán de manera exacta la pregunta? Observa los <em>picos</em> en el eje horizontal.<br />
</li>
<li>¿Podemos esperar que los encuestados hayan sido escogidos de manera aleatoria de aquellos que son elegibles? Posiblemente, pero ¿podemos esperar los que aceptaron la encuesta son representativos?<br />
</li>
<li>¿Podemos asegurar que la muestra de encuestados representa la población adulta del país?</li>
</ul>
</div>

## Hacia el trabajo como **Juez** {-}

Este papel lo tomamos cuando queremos describir algo más allá de los datos que observamos.
Para esto necesitamos realizar *inferencia inductiva.* El peligro es que la inducción es un proceso
generalmente lleno con incertidumbre, pues implica tomar instancias muy particulares para poder emitir
juicios generales. En cambio, el trabajo *deductivo* considera una secuencia de
implicaciones lógicas que nos llevan de generalidades a casos particulares.

Posiblemente, a lo largo de su preparación, o en cursos anteriores de estadística, han
considerado los casos cuando los datos que observamos son seleccionados al azar de la población
objetivo. Sin embargo, esto raramente sucede en la vida real, y es por esto que es necesario considerar
el proceso desde la captura de datos hasta la población objetivo. Por ejemplo, la población de adultos
en Reino Unido con una vida sexual activa.

En general nos interesa que nuestros datos sean:   

- Confiables, es decir, con poca variabilidad y que sea un evento repetible.   
- Válidos, en el sentido que en verdad se esté midiendo lo que queremos medir, y que no haya sesgos.   

Por otro lado, para poder asegurar que la muestra sea adecuada y nos permita observar
de manera fiable la población necesitamos que el estudio tenga *validez interna.* La forma
más efectiva de reducir el sesgo es por medio de **muestreo aleatorio.**

Por último, nos interesa que haya *validez externa* en los datos, lo cual significa que en verdad
nuestras unidades de observación representen la poblacion de interés.

#### Proceso generador de datos {-}

Es por esto que entre las preguntas que se debe hacer el analista de datos una fundamental es
en entender el **proceso generador de datos**, pues esto determinará qué
otras preguntas son relevantes, tanto en términos prácticos como estadísticos.

* La **inferencia estadística** busca hacer afirmaciones, cuantificadas de
manera probabilista, acerca de datos que no tenemos, usando regularidades y
conocimiento de datos que sí tenemos disponibles y métodos cuantitativos.

* Para hacer afirmaciones inferenciales **eficientes y bien calibradas** (con
  garantías estadísticas de calibración) a preguntas donde queremos generalizar de
muestra a población, se requiere conocer con precisión el proceso que genera los
datos muestrales.

* Esto incluye saber con detalle cómo se seleccionaron los datos a partir de
los que se quiere hacer inferencia.

En este caso, eficiente quiere decir que aprovechamos toda la información que
está en los datos observados de manera que nuestros rangos de incertidumbre son
lo más chico posibles (además de estar correctamente calibrados).

Por su parte, probabilísticamente bien calibrados se refiere a que, lo que 
decimos que puede ocurrir con 10% de probabilidad ocurre efectivamente 1 de cada 
10 veces, si decimos 20% entonces ocurre 2 de 10, etc.

Veremos que para muestras dadas naturalmente, a veces es muy difiícil entender a
fondo el proceso generación de la muestra.

#### Ejemplo: Prevalencia de anemia {-}



Supongamos que nos interesa conocer el porcentaje de menores en edad escolar,
(entre 6 y 15 años), con
anemia en México. La fuente de datos disponible corresponde a registros del IMSS
de hospitalizaciones de menores, ya sea por anemia o
que por otra causa (infecciones gastrointestinales, apendicitis, tratamiento de
                    leucemia, ...), se registró
si el menor tenía anemia. En nuestra muestra el 47% de los niños tiene anemia.


```r
head(paciente)
```

```
## # A tibble: 6 x 4
##    edad padecimiento           sexo   anemia
##   <int> <chr>                  <chr>   <int>
## 1     7 infección respiratoria mujer       0
## 2    10 infección respiratoria hombre      0
## 3     7 infección respiratoria hombre      0
## 4     7 infección intestinal   hombre      0
## 5     9 úlcera                 mujer       0
## 6     8 asma                   hombre      0
```


- ¿Qué nos dice esta cantidad acerca de la anemia en la población de menores de edad en la república mexicana?  
  - ¿Podemos hacer inferencia estadística?  
  - ¿Cómo calculamos intervalos de confianza?  


```r
# Si calculo el error estándar de la p estimada como sigue, es correcto?
p <- mean(paciente$anemia)
sqrt(p * (1 - p) / 5000)
```

```
## [1] 0.007042372
```


En la situación ideal diseñaríamos una muestra aleatoria de menores de edad,
por ejemplo, utilizando el registro en educación primaria de la SEP, y
mediríamos la prevalencia de anemia en la muestra, usaríamos esta muestra para
estimar la prevalencia en la población y tendríamos además las herramientas
para medir la incertidumbre de nuestra estimación (reportar intervalos,
                                                   o errores estándar).


En el caso de prevalencia de anemia, discutiendo con médicos e investigadores
nos informan que la anemia se presenta en tasas más altas en niños más chicos.


```r
paciente %>%
  count(edad) %>%
  mutate(prop = round(100 * n / sum(n)))
```

```
## # A tibble: 10 x 3
##     edad     n  prop
##    <int> <int> <dbl>
##  1     6  1000    20
##  2     7   927    19
##  3     8   979    20
##  4     9   446     9
##  5    10   487    10
##  6    11   491    10
##  7    12   246     5
##  8    13   238     5
##  9    14    91     2
## 10    15    95     2
```

Y consultando con las proyecciones de población notamos que los niños chicos
están sobre-representados en la muestra. Lo que nos hace considerar que debemos
buscar una manera de ponderar nuestras observaciones para que reflejen a la
población de niños en el país.

Más aún, investigamos que algunas enfermedades están asociadas a mayor
prevalencia de anemia:


```r
paciente %>%
  count(padecimiento) %>%
  arrange(-n)
```

```
## # A tibble: 7 x 2
##   padecimiento               n
##   <chr>                  <int>
## 1 infección respiratoria   746
## 2 úlcera                   723
## 3 mordedura de perro       722
## 4 asma                     712
## 5 picadura alacrán         705
## 6 apendcitis               702
## 7 infección intestinal     690
```

Utilizamos esta información para modelar y *corregir* nuestra estimación
original. Por ejemplo con modelos de regresión. Sin embargo,
debemos preguntarnos:

  - ¿Hay más variables qué nos falta considerar?  
  - Nuestras estimaciones están bien calibradas?

#### Población {-}

Hasta ahora hemos hablado de muestras de datos. Es un caso muy común en las encuestas. Sin embargo,
también es posible econtrar casos dónde tengamos acceso a todo el conjunto de datos de interés. Ejemplos
de esto son los casos de donde los casos se registran de manera continua como estudios de compras en línea, o
históricos transaccionales en un banco.

Aún en estas situaciones hay que considerar evaluar si en verdad todo lo que nos interesa se registra.
Por ejemplo, las carpetas de investigación de crímenes en la ciudad de México: ¿contienen el reporte de
todos los posibles crímenes en la ciudad?

#### Distribuciones {-}

Hasta ahora hemos mencionado el concepto de distribución como el patrón que presentan los datos
(valores centrales, dispersión, rango, etc.). A esta distribución le llamamos **distribución muestral**
o **empírica**. En general, esperamos que nuestros datos tengan las mismas características (estadísticas)
que la población de donde provienen. Por ejemplo, cuando un fenómeno es generado por pequeñas influencias
hablamos de la distribución Normal o Gaussiana a nivel teórico. El siguiente ejemplo es tomado del libro de
@spiegelhalter2019art, y es sobre el peso de los bebés al nacer para poblaciones caucásicas.


<img src="03-tipos-de-estudio_files/figure-html/unnamed-chunk-12-1.png" width="95%" style="display: block; margin: auto;" />

Entonces, podemos pensar en la población como un conjunto de individuos que provee
la distirbución de probabilidad de una observación aleatoria. Esto será muy útil
cuando lleguemos al momento de hacer *inferencia estadística.* Cuyo objetivo es
poder hacer afirmaciones sobre las características como media, moda, o dispersión que
en general no sabemos de antemano.

En los casos donde no hay muestreo (análisis de crímenes en una ciudad, o estudios
censales) la diferencia entre población y muestra no existe. Sin embargo, la noción
de población es valiosa. Pero, ¿cómo definimos una población?


<div class="comentario">
<p>Existen tres tipos de población de la cual podemos extraer una muestra de forma aleatoria:</p>
<ul>
<li>Población <em>literal.</em> Cuando podemos identificar a un grupo de dónde extraer muestras aleatorias.<br />
</li>
<li>Población <em>virtual.</em> Cuando tomamos observaciones del ambiente, por ejemplo tomar mediciones de la calidad de aire. Los datos generados por en este escenario se denominan <strong>muestras observacionales.</strong><br />
</li>
<li>Población <em>metafórica.</em> Cuando no hay grupo de individuos mas grande. Pero aún asi podemos pensar como si los datos provienen de un espacio imaginario de posibilidades. Los datos geberados en este escenario se denominan <strong>muestras naturales.</strong></li>
</ul>
</div>

## Trabajando como **Juez** {-}

Ahora nos enfocaremos en interpretar resultados estadísticos. Para esto
consideraremos el contexto de  **dos muestras**. Es una práctica común en el análisis
estadístico pues permite contrastar el efecto de un diseño o prueba. Ejemplos
de esto los vemos al medir la tasa de captura en diseño de páginas *web*, pruebas
de una nueva medicina, o simple contraste entre dos poblaciones con
características distintas.



Una *respuesta* a una pregunta de interés
viene acomapañada de una *medida de incertidumbre*, la cual se basa en una
*modelo de probabilidad*. Hay veces en el que el modelo de probabilidad está
bien justificado, pues hay un *mecanismo aleatorio* detrás ---pensemos en el
lanzamiento de una moneda. En otras ocasiones el modelo de probabilidad es un
*artefacto* matemático que asemeja la realidad y permite aplicar
*modelos estadísticos*.

Para entender y comunicar las conclusiones de un modelo hay que estar
conscientes del mecanismo aleatorio que se utilizó, por ejemplo, en la selección
de unidades muestrales, o del grupo al que pertenecen.

Hay dos formas de hacer inferencia. La **inferencia causal** y la **inferencia a
poblaciones**. Saber los mecanismos que generaron los datos nos permite saber
qué tipo de inferencia es más adecuada para el estudio en cuestión.

### Inferencia Causal {-}

En un **experimento aleatorizado** la investigadora asume el control de
asignación de cada unidad experimental a los distintos grupos de estudio por
medio de un mecanismo aleatorio, por ejemplo, una moneda.

En un **estudio observacional** la asignación a los grupos se encuentra fuera del
control de la investigadora.

Es natural cuestionar si por medio de análisis estadísticos podemos concluir
relaciones causales. La respuesta es:

<div class="comentario">
<p>Las relaciones de causa y efecto se pueden inferir sólo si se utiliza un estudio aleatorizado, pero no por medio de estudios observacionales.</p>
</div>

El componente aleatorio asegura que las unidades observacionales con diferentes
características se mezclen, y cualquier evidencia de dicha relación se muestra
en el estudio. Aún asi, no hay certeza absoluta de la presencia de la
relación causal. Dicha incertidumbre es la que usualemente se pretende inculuir
en el modelo a través de técnicas estadísticas.

En un estudio observacional es imposible concluir una relación causal por medio
de un análisis estadístico. La analista no puede asegurar la ausencia de algún
factor de confusión (*confounding variable*) que sea responsable de distorsionar
las conclusiones.

<div class="comentario">
<p>Un factor de confusión está asociado tanto a la pertenencia de un grupo de estudio como al resultado del estudio mismo. La presencia de un factor de confusión no permite relacionar de manera directa la consecuencia con la pertenencia al grupo.</p>
</div>

#### El valor de estudios observacionales {-}

Incluso aunque no podamos establecer relaciones causa-efecto, los estudios observacionales
poseen valor en un estudio formal. Las ventajas se pueden resumir en:

1. El objetivo del estudio. A veces establecer relaciones de causa-efecto no es el objetivo
2. Establecer la relacion causa-efecto se puede hacer por medio de otras rutas.
3. Datos observacionales pueden sugerir nuevas direcciones de investigación a través de *evidencia*.

#### Ejemplo: Policías y tráfico {-}

Supongamos que nos preguntan en cuánto reduce un policía el tráfico en
un crucero grande de la ciudad. La cultura popular
ha establecido que los policías en cruceros hacen más tráfico porque
no saben mover los semáforos.

Nosotros decidimos buscar datos para entender esto. Escogemos
entonces un grupo de cruceros problemáticos, registramos el tráfico
cuando visitamos, y si había un policía o no.

Después de este esfuerzo, obtenemos los siguientes datos:


```
## # A tibble: 10 x 2
## # Groups:   policia [2]
##    policia tiempo_espera_min
##      <int>             <dbl>
##  1       0              2.27
##  2       0              2.65
##  3       0              3.4 
##  4       0              0.39
##  5       0              1.1 
##  6       1             10.8 
##  7       1              4.67
##  8       1              7.77
##  9       1              6.3 
## 10       1              6.99
```

Lo que sabemos ahora es que la presencia de un policía es indicador
de tráfico alto. El análisis prosiguiría calculando medias y medidas de error
(escogimos una muestra aleatoria):

<img src="03-tipos-de-estudio_files/figure-html/unnamed-chunk-17-1.png" width="70%" style="display: block; margin: auto;" />

Si somos ingenuos, entonces podríamos concluir que los policías efectivamente
empeoran la situación cuando manipulan los semáforos, y confirmaríamos la
sabiduría popular.

Para juzgar este argumento desde el punto de vista causal, nos preguntamos primero:

  - ¿Cuáles son los contrafactuales (los contrafactuales explican qué pasaría si hubiéramos
    hecho otra cosa que la que efectivamente hicimos)
    de las observaciones?

#### El estimador estándar {-}

A la comparación anterior ---la diferencia de medias de tratados y no tratados--- le llamamos usualmente el _estimador estándar_ del efecto causal. Muchas veces este es un estimador malo del efecto causal.

En nuestro ejemplo, para llegar a la conclusión errónea que confirma la sabiduría popular, hicimos un supuesto importante:

- En nuestra muestra, los casos con policía actúan como contrafactuales de los casos sin policía.
- Asi que asumimos que los casos con policía y sin policía son similares, excepto por la existencia o no de policía.

En nuestro ejemplo, quizá un analista más astuto nota que tienen
categorías históricas de qué tan complicado es cada crucero. Con esos datos obtiene:


```
## # A tibble: 10 x 3
## # Groups:   policia [2]
##    policia tiempo_espera_min categoria 
##      <int>             <dbl> <fct>     
##  1       0              2.27 Fluido    
##  2       0              2.65 Fluido    
##  3       0              3.4  Típico    
##  4       0              0.39 Fluido    
##  5       0              1.1  Fluido    
##  6       1             10.8  Complicado
##  7       1              4.67 Típico    
##  8       1              7.77 Complicado
##  9       1              6.3  Complicado
## 10       1              6.99 Típico
```

El analista argumenta entonces que los policías se enviaron principalmente a cruceros que
se consideran _complicados_ según datos históricos. Esto resta credibilidad a la
comparación que hicimos inicialmente:

- La comparación del estimador estándar no es de peras con peras: estamos comparando qué efecto tienen los
policías en cruceros difíciles con cruceros no difíciles donde no hay policía.
- La razón de esto es que el proceso generador de los datos incluye el hecho de que no
se envían policías a lugares donde no hay tráfico.
- ¿Cómo producir contrafactuales hacer la comparación correcta?


#### Experimentos tradicionales {-}

Idealmente, quisiéramos observar un mismo crucero en las dos condiciones: con y sin policías. Esto no es posible.

En un experimento "tradicional", como nos lo explicaron en la escuela, nos
aproximamos a esto preparando dos condiciones idénticas, y luego alteramos cada una de ellas
con nuestra intervención. Si el experimento está bien hecho, esto nos da observaciones
en pares, y cada quien tiene su contrafactual.

La idea del experimiento tradicional es _controlar_ todos los factores
que intervienen en los resultados, y sólo mover el tratamiento para producir
los contrafactuales. Más en general, esta estrategia consiste en hacer
_bloques_ de condiciones, donde las condiciones son prácticamente idénticas dentro e cada bloque. Comparamos entonces unidades tratadas y no tratadas
dentro de cada bloque.

Por ejemplo, si queremos saber si el tiempo de caída libre es diferente para un objeto
más pesado que otro, prepararíamos dos pesos con el mismo tamaño pero de peso distinto. Soltaríamos los dos al mismo tiempo y compararíamos el tiempo de caída de cada uno.

En nuestro caso, como es usual en problemas de negocio o sociales, hacer esto es considerablemente más difícil. No podemos "preparar" cruceros con condiciones idénticas. Sin embargo, podríamos intentar bloquear los cruceros
según información que tenemos acerca de ellos, para hacer más comparaciones e peras con peras.

#### Bloqueo {-}

Podemos acercanos en lo posible a este ideal de experimentación usando
información existente.

En lugar de hacer comparaciones directas entre unidades que recibieron
el tratamiento y las que no (que pueden ser diferentes en otros
                             aspectos, como vimos arriba),
podemos refinar nuestras comparaciones _bloquéandolas_ con variables
conocidas.

En el ejemplo de los policías, podemos hacer lo siguiente: dentro de
_cada categoría de cruceros_ (fluido, típico o complicado), tomaremos una muestra de cruceros, algunos con
policía y otros sin. Haremos comparaciones dentro de cada categoría.

Obtenemos un muestra con estas características (6 casos en cada categoría
                                                de crucero, 3 con policía y 3 sin policía):

categoria     policia    n
-----------  --------  ---
Fluido              0    3
Fluido              1    3
Típico              0    3
Típico              1    3
Complicado          0    3
Complicado          1    3


Y ahora hacemos comparaciones dentro de cada bloque creado por categoría:


```
## # A tibble: 3 x 3
## # Groups:   categoria [3]
##   categoria  `policia =0` `policia =1`
##   <fct>             <dbl>        <dbl>
## 1 Fluido              2.1          0.8
## 2 Típico              5.6          4.2
## 3 Complicado         10.4          8.6
```

Y empezamos a ver otra imagen en estos datos: comparando tipos
e cruceros similares, los que tienen policía tienen tiempos de
espera ligeramente más cortos.

¿Hemos termniado? ¿Podemos concluir que el efecto de un policía
es beneficiosos pero considerablemente chico? ¿Qué problemas
puede haber con este análisis?

#### Variables desconocidas {-}

El problema con el análisis anterior es que controlamos por una
variable que conocemos, pero muchas otras variables pueden estar
ligadas con el proceso de selección de cruceros para enviar policías.

- Por ejemplo, envían o policías a cruceros _Típicos_ solo cuando
reportan mucho tráfico.
- No envían a un polícia a un crucero _Complicado_ si no presenta demasiado
tráfico.
- Existen otras variables desconocidas que los tomadores de decisiones
usan para enviar a los policías.

En este caso, por ejemplo, los expertos hipotéticos
nos señalan que hay algunos
cruceros que aunque problemáticos a veces, su tráfico se resuelve
rápidamente, mientras que otros tienen tráfico más persistente, y
prefieren enviar policías a los de tráfico persistente. La lista
de cruceros persistentes están en una hoja de excel que se comparte
de manera informal.

En resumen, no tenemos conocimiento detallado del **proceso generador
de datos** en cuanto a cómo se asignan los policías a los cruceros.

Igual que en la sección anterior, podemos cortar esta complejidad
usando **aleatorización**.

Nótese que los expertos no están haciendo nada malo: en su trabajo
están haciendo el mejor uso de los recursos que tienen. El problema
es que por esa misma razón no podemos saber el resultado de sus esfuerzos,
y si hay maneras de optimizar la asignación que hacen actualmente.

#### Aleatorizando el tratamiento {-}

Tomamos la decisión entonces de hacer un experimento que incluya
aletorización.

En un día
particular, escogeremos algunos cruceros.
Dicidimos usar solamente cruceros de la categoría _Complicada_ y
_Típica_, pues
esos son los más interesantes para hacer intervenciones.

Usaremos un poco de código para entener el detalle: en estos datos,
tenemos para cada caso los dos posibles resultados ipotéticos
$y_0$ y $y_1$ (con
               policia y sin policia). En el experimento asignamos el
tratamiento al azar:


```r
muestra_exp <- trafico_tbl %>% filter(categoria != "Fluido") %>%
  sample_n(200) %>%
  # asignar tratamiento al azar, esta es nuestra intervención:
  mutate(tratamiento_policia = rbernoulli(length(y_0), 0.5)) %>%
  # observar resultado
  mutate(tiempo_espera_exp = ifelse(tratamiento_policia ==1, y_1, y_0))
```

Nótese la diferencia si tomamos la asignación natural del tratamiento (policía o no):


```r
set.seed(134)
muestra_natural <- trafico_tbl %>% filter(categoria != "Fluido") %>%  
  sample_n(200) %>%
  # usamos el tratamiento que se asignó
  # policia indica si hubo o no policía en ese crucero
  # observar resultado
  mutate(tiempo_espera_obs = ifelse(policia ==1, y_1, y_0))
```


Resumimos nuestros resultados del experimento son:


```
## # A tibble: 2 x 3
## # Groups:   categoria [2]
##   categoria  `policia=0` `policia=1`
##   <fct>            <dbl>       <dbl>
## 1 Típico            6.24        4.97
## 2 Complicado       15.8         8.47
```

Sin embargo, la muestra natural da:


```
## # A tibble: 2 x 3
## # Groups:   categoria [2]
##   categoria  `policia=0` `policia=1`
##   <fct>            <dbl>       <dbl>
## 1 Típico            5.49        4.35
## 2 Complicado       10.8         8.93
```

**¿Cuál de los dos análisis da la respuesta correcta a la pregunta:
  ayudan o no los policías a reducir el tráfico en los cruceros
problemáticos?** El experimento establece que un policía en promedio
reduce a la mitad el tiempo de espera en un crucero complicado.

### Inferencia a poblaciones {-}

La situación es bastante clara. Inferir características de una poblacion **sólo** se puede realizar por
medio de muestreo aleatorio, no de otra forma.  

Seleccionar de manera aleatoria significa que cualquier conjunto de tamaño $N$ que escojamos tiene
la misma probabilidad de ser escogido que cualquier otro conjunto del mismo tamaño.


### Resumen: selección de unidades y tratamiento {-}

Vimos dos tipos de inferencia que requieren distintos diseños de estudio,
en particular debemos considerar el mecanismo de aleatorización para
entender las inferencias que podemos hacer: causal o a poblaciones.

El punto crucial para entender las medidas de incertidumbre estadística es
visualizar de manera hipotética, replicaciones del estudio y las condiciones
que llevaron a la selección de la muestra. Esto es, entender el proceso
generador de datos e imaginar replicarlo.

![Inferencia estadística de acuerdo al tipo del diseño [@ramsey]](images/03_inferencia-estudio.png)

* El cuadro en la esquina superior izquierda es donde el análisis es más simple y los
resultados son más fáciles de interpretar.

* Es posible hacer análisis fuera de este cuadro, pero el proceso es más
complicado, requieren más supuestos, conocimiento del dominio y habilidades
de análisis. En general resultan conclusiones menos sólidas. Muchas veces no
nos queda otra opción más que trabajar fuera del cuadro ideal.

<div class="ejercicio">
<p>Ubica los siguientes tipos de análisis:</p>
<ul>
<li>Pruebas clínicas para medicinas</li>
<li>Analizar cómo afecta tener seguro médico a los ingresos, usando datos del ENIGH.</li>
<li>Estimación de retorno sobre inversión en modelos de marketing mix.</li>
</ul>
</div>

#### Asignación natural del tratamiento {-}

- Cuando consideramos un sistema donde se "asignan" tratamientos,
generalmente los tratamientos se asignan bajo un criterio de
optimización o conveniencia.

- La cara buena de este hecho es que de alguna forma los resultados
están intentando optimizarse, y la gente está haciendo su trabajo.

- La cara mala de este hecho es que no podemos evaluar de manera simple la
efectividad de los tratamientos. Y esto hace difícil **optimizar** de forma
cuantificable los procesos, o **entender** qué funciona y qué no.








<!-- ### Experimentos y datos observacionales {-} -->
