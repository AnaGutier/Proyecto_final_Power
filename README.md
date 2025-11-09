# Análisis Exploratorio de Datos: Impacto de la Pandemia COVID-19

## Descripción del proyecto y objetivos

Este proyecto presenta un Análisis Exploratorio de Datos (EDA) sobre el impacto de la pandemia de COVID-19 a nivel mundial. El objetivo principal es investigar la posible relación entre el perfil socioeconómico y de bienestar de un país y la severidad del impacto de la pandemia, medido a través de la métrica de "muertes en exceso".

Para ello, se integran dos fuentes de datos distintas:
1.  Un registro temporal del exceso de muertes acumuladas durante la pandemia.
2.  El Informe Mundial de la Felicidad de 2021, que proporciona indicadores socioeconómicos clave por país.

Los objetivos específicos del análisis son:
- Realizar una limpieza y transformación exhaustiva de los datos para asegurar su calidad y consistencia.
- Explorar las tendencias temporales de la pandemia a nivel global y regional.
- Analizar si existe una correlación entre indicadores como el PIB, la esperanza de vida o el soporte social y el impacto de la pandemia.
- Identificar patrones, casos atípicos (outliers) y perfiles de países que hayan demostrado ser especialmente resilientes o vulnerables.
- Extraer conclusiones basadas en evidencia que puedan servir como base para futuros análisis.

## Instalaciones y requisitos técnicos

- **Lenguaje:** Python 3.8+
- **Librerías Principales:**
  - `pandas`
  - `numpy`
  - `matplotlib`
  - `seaborn`
  - `scikit-learn`
- **Entorno:** Se recomienda utilizar un entorno virtual para gestionar las dependencias.

Para instalar todas las librerías necesarias, puede ejecutar el siguiente comando en su terminal, asegurándose de usar el ejecutable de Python asociado a su entorno de trabajo:

```bash
pip install pandas numpy matplotlib seaborn scikit-learn
```

Para ejecutar el análisis, abra el notebook `Program_EDA.ipynb` y ejecute las celdas en orden secuencial.

/////////////////////////////////////////////////////////////////////////////////
1. Fuentes de datos
1º Archivo (Estimated cumulative excess deaths per 100,000 people during COVID-19, Jan 1, 2020 to Sep 28, 2025):
Entity: El nombre del país o región.

Day: La fecha del registro.

Cumulative excess deaths per 100,000 people (central estimate): La estimación acumulada más probable de "muertes en exceso" por cada 100,000 personas. Es la métrica principal para ver el impacto real de la pandemia más allá de las cifras oficiales de COVID-19.

Cumulative excess deaths per 100,000 people (95% CI, lower bound): Límite inferior del intervalo de confianza para la estimación anterior.

Cumulative excess deaths per 100,000 people (95% CI, upper bound): Límite superior del intervalo de confianza para la estimación anterior.

Total confirmed deaths due to COVID-19 per 100,000 people: El total acumulado de muertes confirmadas oficialmente por COVID-19 por cada 100,000 personas.

2º Archivo (World Happiness Index Report 2021):
Country name: El nombre del país.

Regional indicator: Región a la que pertenece el país (Europa del este, África subsahariana, Asia oriental, etc.).

Ladder score: Puntuación de Felicidad. Es la métrica principal, basada en encuestas donde la gente valora su vida en una escala de 0 a 10. Standard error of ladder score: El error estándar de la puntuación de felicidad, que indica la incertidumbre en la estimación.

upperwhisker / lowerwhisker: Los límites superior e inferior del intervalo de confianza del 95% para el Ladder score.

Logged GDP per capita: El Producto Interior Bruto (PIB) per cápita, en escala logarítmica. Mide la riqueza económica.

Social support: La percepción de tener apoyo social (familia, amigos) en momentos de necesidad.

Healthy life expectancy: La esperanza de vida media con buena salud.

Freedom to make life choices: La percepción de libertad para tomar decisiones importantes en la vida.

Generosity: La generosidad de la población, medida por donaciones recientes a la caridad.

Perceptions of corruption: La percepción sobre el nivel de corrupción en el gobierno y las empresas.

Ladder score in Dystopia: Una puntuación de referencia de un país hipotético llamado "Distopía", que tiene los peores valores en todos los factores. Sirve como un punto de comparación base.

Explained by: [Factor]: 6 columnas desglosan cuánto contribuye cada uno de los 6 factores principales a la puntuación de felicidad de cada país. Estos factores (y por tanto, columnas) son:

Log GDP per capita
Social support
Healthy life expectancy
Freedom to make life choices
Generosity
Perceptions of corruption
Dystopia + residual: La parte de la puntuación de felicidad que no se explica por los 6 factores anteriores. Es la suma de la puntuación de la "Distopía" y el "residuo" (lo que queda sin explicar).
///////////////////////////////////////////////////////////////////////////777777


## Informe del Análisis

A continuación, se detalla el proceso de análisis, desde las hipótesis iniciales hasta los hallazgos clave.

### Hipótesis Iniciales

El análisis partió de las siguientes suposiciones iniciales:
1.  **Hipótesis 1:** Los países con mejores indicadores socioeconómicos (mayor PIB per cápita, mayor esperanza de vida y mejor soporte social) habrían gestionado mejor la pandemia, resultando en un menor número de muertes en exceso.
2.  **Hipótesis 2:** El impacto de la pandemia, tanto en su cronología como en su intensidad, variaría significativamente entre las diferentes regiones geográficas del mundo.
3.  **Hipótesis 3:** La calidad institucional, medida de forma indirecta a través de la percepción de corrupción, estaría correlacionada con los resultados, esperando que una menor corrupción se asocie a un menor impacto.

### Proceso, Complicaciones y Limitaciones

El proceso de preparación de datos fue un paso crítico que presentó varios desafíos:

1.  **Fusión de Datos:** La complicación inicial fue unir los dos datasets, ya que la columna de país tenía nombres diferentes (`Entity` vs. `Country name`). Además, el dataset de muertes contenía entidades que no eran países (continentes, regiones económicas), lo que generaba un 43% de filas sin correspondencia.
    - **Solución:** Se normalizaron los nombres de las columnas y se utilizó el dataset de felicidad como una "lista maestra" para filtrar el de muertes, asegurando que solo se analizaran países válidos. Se eligió una unión `left` para no perder ninguna observación temporal de los países válidos.

2.  **Gestión de Valores Nulos:** Se identificaron valores nulos en varias columnas.
    - **Solución:** Se aplicó una estrategia específica para cada caso: se rellenaron con `0` los nulos iniciales de muertes confirmadas, se imputó la `mediana` en columnas socioeconómicas con pocos datos faltantes, y se eliminaron las filas donde la métrica principal (`Cumulative excess deaths...`) era nula, ya que carecían de valor analítico.

3.  **Limitaciones del Análisis:**
    - **Datos Estáticos vs. Dinámicos:** Se está correlacionando una "foto" de los indicadores socioeconómicos de 2021 con datos de la pandemia que abarcan varios años. Estos indicadores no son estáticos en la realidad.
    - **Correlación no es Causalidad:** El análisis identifica relaciones y patrones, pero no puede probar de forma concluyente que un factor sea la causa de otro.
    - **Datos como Estimaciones:** La métrica de muertes en exceso es una estimación estadística, no un recuento exacto, y por tanto tiene un margen de error inherente.

### Explicación de Gráficos, Patrones y Hallazgos Clave

A lo largo del EDA, se generaron varias visualizaciones para extraer insights:

- **Análisis Temporal (Global y Regional):**
  - **Gráfico de Líneas Global:** Mostró que la pandemia tuvo olas globales claras, con picos de mortalidad en momentos específicos.
  - **Gráfico de Líneas por Región:** Este análisis más profundo reveló que las dinámicas fueron muy diferentes entre regiones. Sudamérica y Europa del Este, por ejemplo, mostraron picos de muertes en exceso mucho más altos y prolongados que regiones como el Sudeste Asiático o Australia/Nueva Zelanda. Esto invalida la idea de una experiencia pandémica monolítica.

- **Análisis de Correlación (Heatmap y Scatter Plots):**
  - **Hallazgo Clave:** Se refutó la versión más simple de la Hipótesis 1. El mapa de calor y los gráficos de dispersión mostraron que **no existe una relación lineal simple entre el PIB de un país y el número de muertes en exceso**. La correlación es débil.
  - **Patrón Identificado:** La `Healthy life expectancy` (esperanza de vida) mostró una ligera correlación negativa con las muertes, pero la dispersión de los datos era muy alta, indicando que otros factores estaban en juego.

- **Análisis de Outliers (Casos Atípicos):**
  - **Técnica:** Se utilizó un modelo de regresión simple para predecir las muertes "esperadas" de un país según su PIB. Los países más alejados de esta predicción fueron identificados como outliers.
  - **Hallazgo Clave:** Se identificaron "underperformers" (países con muchas más muertes de las esperadas para su nivel de riqueza, como **Perú, Bolivia y Bulgaria**) y "overperformers" (países con muchas menos muertes, como **Australia, Nueva Zelanda y Taiwán**). Esto sugiere fuertemente que las estrategias nacionales, la geografía y otros factores locales fueron más decisivos que la riqueza por sí sola.

- **Análisis de Cuadrantes (Salud vs. Corrupción):**
  - **Técnica:** Se segmentaron los países en cuatro perfiles basados en su esperanza de vida y percepción de corrupción.
  - **Hallazgo Clave:** Los países en el cuadrante "ideal" (Alta esperanza de vida, Baja corrupción) tuvieron, en promedio, el menor impacto en muertes en exceso, validando parcialmente la Hipótesis 3. Por el contrario, el grupo con "Baja esperanza de vida, Alta corrupción" fue el más afectado.

- **Análisis de Velocidad de Crecimiento:**
  - **Hallazgo Clave:** Se identificaron los países que sufrieron las olas más "explosivas" (mayor incremento de muertes en un solo día). Este ranking es diferente al de muertes totales acumuladas, aportando una nueva dimensión al concepto de "impacto".

## Resultados y Conclusiones

El análisis exploratorio de datos ha revelado que el impacto de la pandemia de COVID-19 es un fenómeno complejo y multifactorial que no puede explicarse con variables socioeconómicas simples de forma aislada.

1.  **La riqueza no fue un escudo protector:** La hipótesis de que los países más ricos gestionarían mejor la pandemia queda refutada. La relación entre PIB y muertes en exceso es débil y está llena de excepciones.
2.  **La geografía y la región son determinantes:** El factor más claro de agrupación es el `Regional indicator`. Las dinámicas de la pandemia fueron marcadamente regionales.
3.  **Las estrategias nacionales importan:** La existencia de outliers tan pronunciados (países que lo hicieron mucho mejor o peor de lo esperado) es la evidencia más fuerte de que las políticas públicas, la preparación del sistema de salud, la demografía y las decisiones de cada gobierno fueron probablemente los factores más decisivos.
4.  **La calidad institucional parece influir:** El análisis de cuadrantes sugiere que una buena gobernanza (baja corrupción) combinada con un sistema de salud robusto (alta esperanza de vida) es el perfil más resiliente ante una crisis de esta magnitud.

En resumen, el análisis nos aleja de conclusiones simplistas y nos orienta hacia una visión donde el contexto local y la toma de decisiones son primordiales.

## Próximos pasos a seguir

Basado en los hallazgos y limitaciones de este EDA, los siguientes pasos lógicos serían:

1.  **Análisis Cualitativo de Outliers:** Realizar una investigación documental sobre las políticas sanitarias y sociales implementadas en los países identificados como "overperformers" y "underperformers" para entender las causas de su éxito o fracaso.
2.  **Incorporar Variables Dinámicas:** Enriquecer el dataset con datos que varíen en el tiempo, como las tasas de vacunación por país, los índices de movilidad o las fechas de implementación de confinamientos, para un análisis de series temporales más sofisticado.
3.  **Modelado Estadístico Avanzado:** Construir un modelo de regresión múltiple o de machine learning para cuantificar el peso de cada variable y controlar los efectos de confusión, permitiendo aislar el impacto de factores específicos de una manera más robusta.