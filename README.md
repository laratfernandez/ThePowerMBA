# Dashboard & Análisis de Datos 
La adicción a la hora de dormir: deuda de sueño y tiempo frente a la pantalla

## Contexto y problema de negocio
La arquitectura del sueño en la actualidad se ve gravemente alterada por el consumo de contenido digital a altas horas de la noche. La hiperactivación dopaminérgica provocada por los vídeos de formato corto (TikTok, Reels) y la supresión de la melatonina —causada por la exposición a la luz azul sin ningún tipo de filtro— derivan directamente en una deuda de sueño crónica y en una mayor fatiga al día siguiente.

Este conjunto de datos ofrece telemetría sobre el estilo de vida de 8500 individuos, relacionando sus hábitos tecnológicos nocturnos, su consumo de cafeína y sus cronotipos demográficos con métricas fisiológicas del sueño y los niveles de fatiga resultantes.

### Ingeniería de datos y lógica biofísica
Este conjunto de datos se ha generado mediante una simulación basada en parámetros biofísicos estándar:

  Supresión de melatonina: varía en función del brillo de la pantalla y la duración de la exposición; el uso de filtros de luz azul mitiga este efecto de forma no lineal.
  Niveles de activación (despertar): los distintos tipos de aplicaciones conllevan diferentes grados de activación cognitiva (por ejemplo, los vídeos algorítmicos de formato corto generan una mayor neuroactivación que la lectura digital).
  Variables objetivo: incluye una variable continua (`next_day_fatigue_score`) para tareas de regresión y una variable categórica multiclase (`sleep_debt_category`) para tareas de clasificación.

### Preguntas de investigación iniciales

1.	El coste del «scroll» infinito (*doomscrolling*): ¿es posible cuantificar la reducción exacta en la fase de sueño REM por cada 30 minutos de navegación en dispositivos antes de dormir?
2.	Eficacia de las medidas de mitigación: ¿compensa matemáticamente el uso de un filtro de luz azul la fatiga generada por una pantalla al 100 % de su brillo?


## Comprensión general de los datos, transformación y limpieza.

Hemos importado un archivo CSV a Excel.
En este conjunto de datos, tenemos 18 columnas y 8500 registros. 
User_id - La primera columna recoge el identificador único de usuario. Se ha comprobado y no están duplicados por lo que tenemos un total de 8500 registros únicos. Además, no se encuentran celdas en blanco en ninguna de las columnas.
Las siguientes columnas recogen datos de los usuarios, como: 
-	Edad (age). 
Variable numérica. Desde los 18 hasta los 65 años con una mayor representación del segmento que va de 18 a 45 años. Esta variable será agrupada en el dashboard para facilitar la lectura de los datos.
-	Género (gender). 
Variable categórica. Identidad declarada por la propia persona. Hay tres tipos: mujer, hombre y no binario.
A continuación, encontramos las variables medidas específicamente para el estudio. Dentro de estos datos, hemos tenido que hacer alguna conversión ya que se habían perdido los decimales al descargar el archivo desde Kaggle. 
-	Categoría laboral (occupation_type). 
Variable categórica. Cinco tipos que influirán en las obligaciones relacionadas con el despertar. Sector sanitario/Trabajador por turnos; Corporativo 9-5; Estudiante; Freelance/Creativo y Remoto.
-	Cronotipo (chronotype). 
Variable categórica. Preferencia circadiana biológica. Matutino, intermedio o vespertino. 
-	Minutos de teléfono en la cama antes de dormir (bedtime_phone_minutes). 
Variable numérica. Desde un mínimo de 1 minuto a un máximo de 180 minutos.
-	Aplicación de uso en la cama antes de dormir (primary_bedtime_app).
Variable categórica. Intagram/Reddit; Youtube; TikTok/Reels; Noticias/Lectura; Streaming (Netflix/Hulu) o Mensajería.
-	Porcentaje de brillo de la pantalla (screen_brightness_pct). 
Variable numérica. Del 10% al 100%. 
-	Uso de filtro de luz azul (blue_light_filter_active). 
Variable numérica. Binaria donde 1 es activo y 0 inactivo.
-	Consumo en miligramos de cafeína después de las 5 de la tarde (caffeine_post_5pm_mg). 
Variable numérica. Esta columna tiene muchos valores en 0, por lo que para posteriores estadístico, se convirtieron los 0 por vacío.
-	Minutos de actividad física (physical_activity_min). 
Variable numérica. Va desde 0 a un máximo de 112 minutos. También se convirtieron los 0 por vacíos.
-	Minutos de latencia del sueño (Sleep_latency_min_converted). 
Variable numérica. Minutos estimados necesarios para conciliar el sueño después de guardar el dispositivo. Esta columna tuvimos que convertirla para que nos devolviese un decimal.
-	Total de horas dormidas (Total_sleep_hours_converted). 
Variable numérica. Datos transformados a través de fórmula por alternarse entre uno o dos decimales.  
-	Porcentaje de sueño profundo (Deep_sleep_pct_converted). 
Variable numérica. También transformados de la carga original para añadirle un decimal.
-	Porcentaje de fase REM (Rem_sleep_pct_converted). 
Variable numérica. Columna con datos convertidos de la carga original. 
-	Número de repeticiones de la alarma por la mañana (morning_alarm_snoozes). 
Variable numérica. Va de 1 a 7 veces.
-	Puntuación de fatiga al día siguiente (Next_day_fatigue_score_converted). 
Variable numérica. De 1 a 10. También tuvo que modificarse esta columna.
-	Categoría de deuda de sueño (sleep_debt_category). 
Variable categórica. Cuatro tipos: Déficit leve; deuda moderada; recuperación óptima y grave déficit de sueño.
Además, se añadió la columna de PCT rest of sleep (porcentaje del resto del sueño) que se hizo simplemente restando de un total de 100, las columnas “Sueño REM” y “Sueño profundo”. De tipo numérico.

### Detección de Outliers

Hemos analizado diferentes variables para encontrar relación entre los distintos datos y también el comportamiento de estos dentro de sus rangos.

Por ejemplo, tiempo de uso del teléfono y consumo de cafeína, teniendo en cuenta la desviación estándar comparada con el promedio, vemos que es alta y nos indica que los datos están muy dispersos y posible presencia de outliers. Viendo los valores mínimo y máximo, nos damos cuenta de esta oscilación.

Podemos encontrar muchos outliers en nuestro gráfico con bigotes. Por ejemplo, en el caso de tiempo de uso del teléfono, vemos que la mediana se encuentra alrededor de los 50 minutos, mientras que el promedio se va a los 59 minutos. Se puede observar un comportamiento similar en el caso de consumo de cafeína, incluso con valores más extremos. Los valores outliers modifican considerablemente el promedio de ambas variables.

Pese a esto y especialmente en el caso de tiempo de uso de teléfono, no sería apropiado deshacernos de los valores outliers porque se encuentran tanto a la baja como al alta y además reflejan un comportamiento real de los usuarios.

### Correlaciones

También hemos querido ver cómo se relacionaban unas variables numéricas con otras y para eso hemos calculado diferentes coeficientes de correlación, llegando a la conclusión de que hay cuatro conjuntos de valores que arrojan una correlación algo significativa. 

La primera correlación que nos devuelvo un valor significativo es la de tiempo de uso del móvil antes de dormir, con el tiempo de latencia de inicio del sueño. Esto es muy significativo señalarlo porque una latencia del sueño por encima de 20-30 min está relacionado con dificultades para conciliar el sueño o insomnio, estrés, ansiedad o mal ajuste del ritmo circadiano. Esto último es lo que nos apela en nuestro análisis, pues el uso de pantallas por las noches altera estos ciclos circadianos que regula los procesos físicos, mentales y conductuales.

Con ello podemos relacionar la latencia del sueño con el total de horas dormida. No es una correlación tan fuerte pero sí nos indica cierta tendencia de que a mayor tiempo de latencia, menos horas de sueño.

De manera casi paralela existe la correlación entre el tiempo de uso del teléfono antes de dormir y el total de horas de sueño. A mayor uso del teléfono, menos horas de sueño. Está muy ligada a la correlación anterior (latencia del sueño con horas dormidas) y de hecho indican una correlación similar. 

Por último, encontramos una fuerte correlación entre las horas dormidas y la fatiga posterior. Nos indica que, a mayores horas dormidas, menos fatiga. Esto era de esperar ya que el estudio se hizo con estas variables objetivo en mente para arrojar resultados claros.

Después de todo este análisis y transformación, podemos decir que es un conjunto de datos bastante limpio donde apenas hemos tenido que hacer transformaciones ni eliminar duplicados.

## Informe de verificación de suposiciones

### Resumen

El análisis estadístico nos permite concluir que a priori no se puede cuantificar la reducción de la fase REM por cada 30 minutos de navegación de pantalla, ya que no se ve correlación al venir en porcentaje, pero sí se puede determinar que influye en la latencia y horas de sueño. 

También concluimos que sí que compensa el uso de medidas de mitigación como el filtro de luz azul si el brillo de la pantalla está por encima del 30%. 

Lo que tiene una influencia directa es el tiempo de exposición en la pantalla, reduciendo con ello el tiempo que se tarda en dormir, importante para mantener nuestros ciclos circadianos en niveles normales.

Importante es la aplicación que se utilice antes de dormir, va a influir directamente en el tiempo de latencia y a su vez en la fase REM del sueño, crucial en el equilibrio emocional, la creatividad y la consolidación de la memoria y el aprendizaje. En este caso, el consumo de aplicaciones como TikTok o los formatos de vídeo corto, aun con un tiempo de pantalla similar, alteran considerablemente el tiempo que se tarda en dormir de unas a otras. 

Podemos descartar que la cafeína o la actividad física influyan directamente en las horas de sueño. Lo que sí determina el total de horas de sueño es el tipo ocupación, dónde los trabajadores con turnos rotativos o del sector de la salud, ven considerablemente mermado su tiempo de sueño.

### Descripción de las suposiciones

Tenemos un conjunto de valores que a priori, nos lleva a pensar que hay una causalidad directa entre ellos. Se cree inicialmente, que el porcentaje de brillo de la pantalla, podría influir en la latencia y total de horas dormidas. Lo mismo ocurre con el uso de filtro azul.

El tipo de aplicación también se considera que afecta directamente a la latencia nombrada. 

El tiempo de exposición a la pantalla, se considera que afecta a la Fase REM del sueño.

El consumo de cafeína o la actividad física a las horas dormidas y, en consecuencia, a la fatiga que pueda sentirse al día siguiente.

### Metodología del análisis

Para sacar conclusiones de los datos, además del uso de métodos estadísticos ya descritos con anterioridad (gráficos de dispersión y correlaciones), hemos intentado sacar datos relevantes con diferentes gráficos y tablas. 

Para ello hemos hecho uso de gráficos de líneas, gráficos de barras, gráficos circulares, tablas y big numbers. Además, para ofrecer una mayor flexibilidad a los datos, hemos insertado segmentadores en el dashboard, tanto de datos demográficos, como edad y género, categóricos como ocupación y aplicación más usada como tiempo de uso de pantalla.

### Resultados de gráficos y tablas

Viendo el dashboard, sabemos que hay un análisis en el que participaron un total de 8500 personal, de las cuales 4347 (51%) se identifican como mujeres, 3905 (46%) se identifican como hombres y 248 (3%) se identifican como no binario.

El promedio de uso del teléfono antes de dormir es de 59,25 minutos, es decir casi una hora, en consecuencia, tiene un latencia de promedio de 40,67 minutos y total de horas de sueño en promedio de 6,27 horas. 

El 94,51% de los usuarios hace un minuto o más al día de actividad física, con un promedio de 35,70 mins. Y podemos decir que cerca del 42% de los entrevistados consume cafeína con un promedio de 33,22 miligramos.

Esto es principalmente lo que nos dicen los big numbers. Vamos a prestarle atención a las tablas, y ver que la gráfica de líneas donde se puede ver la relación del porcentaje del brillo de la pantalla con la latencia del sueño, podemos determinar que hay una relación directa, a más uso de brillo, más se tarda en dormir y además se suma el uso de filtro de luz azul, donde con un brillo bajo no se aprecia su injerencia, pero a medida que aumenta el brillo, más notable se hace la diferencia en la latencia del sueño. De manera contraria y más sutil se comporta la variable de sueño profundo, donde a más brillo, menos sueño profundo. Sin embargo, no se aprecia que influya en la fase REM ni en el total de horas de sueño. El segmentador de tiempo de uso de pantallas antes de dormir, nos permite ver la variabilidad que hay en el total de horas de sueño con el aumento de 30 minutos de uso.

Tenemos el gráfico de barras de promedio de horas dormidas según cronotipo que nos confirma que quien se identifica con un cronotipo, su media de horas de sueño se ajusta a ello.

También vemos tres gráficos circulares seguidos, sobre las fases del sueño, que nos indican el promedio de cuánto corresponde a cada fase. También varía con los segmentadores.

En el siguiente gráfico de líneas vemos cómo influye directamente el tiempo de pantalla con las horas de sueño, siendo casi inversamente proporcional, donde a menor tiempo de pantalla, más horas de sueño. 

En el siguiente gráfico de barras hemos añadido el número de veces que se atrasa la alarma con el promedio de horas dormidas. A su vez hemos puesto el porcentaje que representa respecto al total. Esta gráfica resulta muy interesante para utilizar con los segmentadores.

En la tabla de ocupación por horas de sueño, podemos ver como los trabajadores por turnos son los que ven más afectados sus horas de sueño y es probable que no esté relacionado con el uso de pantallas si no con la propia naturaleza de su trabajo. Es el grupo que alberga un menor porcentaje de recuperación óptima del sueño.

La siguiente tabla nos revela que, aunque se hace un tiempo de uso de pantalla similar independientemente de la aplicación, la latencia del sueño varía hasta 13 minutos de media entre leer o ver las noticias y TikTok o Reels. Esto no denota que altere ninguna parte del sueño de manera acuciante, sí que se ven variaciones, en especial en la parte REM, la fase inicial del sueño y por décimas, en las horas dormidas.

### Interpretación y Conclusiones

Concluimos que el uso promedio de pantalla antes de dormir es muy alto, que esto altera considerablemente el ciclo circadiano, por el cual los expertos recomiendan reducir el uso de pantallas hasta 2 o 3 horas antes de acostarnos. 

Además de esto, el tipo de aplicación que se use, también influye, encontrando una alteración del ciclo circadiano mayor con los vídeos de formato corto como Reels de Tiktok, influyendo posteriormente en la fase REM del sueño y el total de horas dormidas.

El tiempo de pantalla influye directamente en las horas totales de sueño y el porcentaje de brillo en el tiempo de latencia pero no se observa un resultado lineal, lo mismo pasa con el uso del filtro azul, donde se observan resultados más marcados en el los porcentajes de brillo altos pero no en los bajos. 

Los trabajadores del sector sanitario o por turnos son los que menos horas de sueño tienen y en general todas las profesiones sufren una deuda del sueño moderada. 

No se observan variaciones acuciantes entre distintos grupos de edad ni de género, lo que nos hace ver este problema como algo compartido de forma intergeneracional.

### Recomendaciones

Lo conveniente sería seguir el consejo de los expertos y evitar el uso de pantallas antes de irse a la cama con un tiempo prudencial para no alterar nuestros ciclos circadianos. Tener una constancia con los horarios de dormir y comer. 

En el caso del uso de pantallas al acostarse, lo ideal sería usarlas con un porcentaje de brillo bajo y evitar el consumo de vídeos de formato corto.
