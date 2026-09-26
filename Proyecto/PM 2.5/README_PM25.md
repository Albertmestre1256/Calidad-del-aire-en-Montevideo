# Calidad del Aire en Montevideo — PM2.5

> Proyecto en curso. Continuación de un análisis que comenzó con NO2 y pasó por O3 — ver [../../README.md](../../README.md) para el recorrido completo, y [../O3/README.md](../O3/README.md) para el detalle de por qué se discontinuó O3.

## Pregunta de investigación

¿Existen diferencias estadísticamente significativas en los niveles de PM2.5 entre las estaciones de monitoreo disponibles, y entre días de semana vs. fines de semana?

## Fuente de datos

Dataset "Red de Monitoreo de la Calidad del Aire de Montevideo" (Portal de Datos Abiertos, CKAN), recomendado directamente por la Unidad Calidad de Aire (SECCA/IMM) por su mayor confiabilidad: datos horarios, con más de 10 años de limpieza acumulada.

- Dataset: https://ckan.montevideo.gub.uy/dataset/red-de-monitoreo-de-la-calidad-del-aire-de-montevideo

## Estado del proyecto

- [x] **Adquisición de datos.** Descarga y filtrado de los 12 archivos anuales (2015-2025) del portal, quedándose solo con el contaminante PM2.5 (código `PM2` en el dataset original). 243.184 filas.
- [ ] **Limpieza y preparación.** En curso — ver hallazgos de calidad de datos abajo.
- [ ] **Estadística descriptiva.**
- [ ] **Estadística inferencial.**
- [ ] **Redacción del reporte final.**
- [ ] **Revisión crítica.**

## Hallazgos de calidad de datos (en curso)

Aun tratándose de un dataset validado y recomendado por la propia Intendencia, se encontraron dos inconsistencias durante la etapa de limpieza:

1. **Formato de archivo inconsistente en el portal.** Los recursos de 2023 y 2024 tienen el campo de formato cargado como `.CSV` (con punto) en vez de `CSV`, lo que puede excluir esos años silenciosamente si se filtra por igualdad exacta de texto.
2. **Nombres de estación inconsistentes entre archivos.** Una misma estación (Ciudad Vieja) aparecía con dos variantes de escritura distintas según el año, fragmentando sus datos en grupos separados al agrupar. Corregido y verificado cruzando con las coordenadas geográficas de cada estación.

Ambos hallazgos, junto con cualquier otro que surja durante la limpieza, se están reportando a la Unidad Calidad de Aire.

## Cómo reproducir el análisis

1. Clonar el repositorio.
2. Instalar las dependencias: `pandas`, `requests`, `matplotlib`, `seaborn`, `scipy`, `pyproj`, `folium`.
3. Ejecutar el notebook de principio a fin. La descarga de datos requiere conexión a internet (usa la API pública de CKAN).
