# Calidad del Aire en Montevideo — PM2.5

> Proyecto en curso. Continuación de un análisis que comenzó con NO2 y pasó por O3 — ver [../../README.md](../../README.md) para el recorrido completo, y [../O3/README_O3.md](../O3/README_O3.md) para el detalle de por qué se discontinuó O3.

## Pregunta de investigación

¿Existen diferencias estadísticamente significativas en los niveles de PM2.5 entre las estaciones de monitoreo disponibles, y entre días de semana vs. fines de semana?

## Fuente de datos

Dataset "Red de Monitoreo de la Calidad del Aire de Montevideo" (Portal de Datos Abiertos, CKAN), recomendado directamente por la Unidad Calidad de Aire (SECCA/IMM) por su mayor confiabilidad: datos horarios, con más de 10 años de limpieza acumulada.

- Dataset: https://ckan.montevideo.gub.uy/dataset/red-de-monitoreo-de-la-calidad-del-aire-de-montevideo
- El dataset mezcla varios contaminantes en un mismo archivo por año (2014-2025); se filtró específicamente por PM2.5 (código `PM2`).

## Estado del proyecto

- [x] **Adquisición de datos.** Descarga y filtrado de los 12 archivos anuales del portal, quedándose solo con PM2.5. 243.184 filas iniciales.
- [x] **Limpieza y preparación.** Ver hallazgos de calidad de datos y exclusiones abajo. Dataset final: **4 estaciones**.
- [x] **Estadística descriptiva.** Tabla de medidas por estación y tipo de día, histogramas (escala completa y con zoom), boxplots por estación (detalle año a año) y boxplot general combinado.
- [ ] **Estadística inferencial.** Próximo paso: test de normalidad y comparación entre grupos.
- [ ] **Redacción del reporte final.**
- [ ] **Revisión crítica.**

## Hallazgos de calidad de datos

Aun tratándose de un dataset validado y recomendado por la propia Intendencia, se encontraron varias inconsistencias durante la limpieza:

1. **Formato de archivo inconsistente en el portal.** Los recursos de 2023 y 2024 tienen el campo de formato cargado como `.CSV` (con punto) en vez de `CSV`, lo que puede excluir esos años silenciosamente si se filtra por igualdad exacta de texto.

2. **Nombres de estación inconsistentes entre archivos.** Dos estaciones de la zona Ciudad Vieja aparecían con variantes de escritura distintas según el año, fragmentando sus datos en grupos separados al agrupar. Corregido y verificado cruzando con el archivo oficial de estaciones y sus coordenadas geográficas.

3. **El identificador de estación (`station_id`) corresponde a una zona, no a un sensor puntual.** Una misma zona (por ejemplo, "Ciudad Vieja") puede tener varios sensores distintos con el mismo `station_id` pero ubicaciones y nombres propios. Esto obliga a verificar siempre el nombre completo de la estación contra el archivo oficial antes de fusionar o comparar datos entre "estaciones" que en realidad podrían ser lugares distintos.

4. **Dos estaciones excluidas del análisis por bajo volumen de datos útiles:**
   - **Tres Cruces 3:** solo tiene registros en 2019 y 2020, con el año 2020 completamente inválido (100% de valores nulos).
   - **Colón:** solo tiene registros útiles en 2017-2018 (poco más de 1.300 mediciones en total), un volumen demasiado bajo para comparaciones significativas con las demás estaciones.

5. **Bloques de meses completos sin datos en algunas estaciones/años**, detectados al notar patrones "demasiado rectos" en los gráficos de evolución mensual (un `lineplot` conecta los puntos existentes sin avisar de los huecos intermedios). Confirmado con una tabla de verificación mes a mes en dos estaciones y tres años puntuales.

6. **Dos sensores de una misma zona en sucesión temporal, no simultánea.** En la zona Ciudad Vieja, un sensor dejó de operar y otro (con nombre distinto) comenzó a funcionar poco después, con una superposición mínima entre ambos. Se tratan como estaciones separadas en el análisis principal, con una mirada complementaria aparte que las muestra en continuidad temporal.

Todos estos hallazgos se están reportando a la Unidad Calidad de Aire, como parte del mismo proceso de verificación aplicado anteriormente a NO2 y O3.

## Hallazgo destacado de la estadística descriptiva

En dos de las cuatro estaciones (Ciudad Vieja 2 y Ciudad Vieja 3), los niveles de PM2.5 son sistemáticamente más altos en días de semana que en fines de semana — un patrón opuesto al encontrado con O3, donde el fin de semana era consistentemente más alto. Es consistente con la naturaleza de cada contaminante: PM2.5 se emite directamente por el tráfico vehicular, mientras que O3 se forma por reacción química y puede acumularse más cuando hay menos tráfico. Esta comparación se desarrollará en la discusión final del análisis.

## Cómo reproducir el análisis

1. Clonar el repositorio.
2. Instalar las dependencias: `pandas`, `requests`, `matplotlib`, `seaborn`, `scipy`, `pyproj`, `folium`.
3. Ejecutar el notebook de principio a fin. La descarga de datos requiere conexión a internet (usa la API pública de CKAN).
