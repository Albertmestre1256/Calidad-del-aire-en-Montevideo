# Calidad del Aire en Montevideo — Análisis Estadístico

Análisis exploratorio e inferencial sobre los niveles de Ozono (O3) en Montevideo, utilizando datos abiertos publicados por la Intendencia de Montevideo. Proyecto personal, realizado como parte de un proceso de fortalecimiento de base cuantitativa en ciencia de datos.

## Pregunta de investigación

¿Existen diferencias estadísticamente significativas en los niveles de O3 entre las dos estaciones de monitoreo disponibles (Colón y Curva de Maroñas), y entre días de semana vs. fines de semana?

## Fuente de datos

Portal de Datos Abiertos de la Intendencia de Montevideo (CKAN), dataset de Ozono (O3) de la Red de Monitoreo de Calidad del Aire, gestionada por el Servicio de Evaluación de la Calidad y Control Ambiental (SECCA).

* Dataset: https://ckan.montevideo.gub.uy/dataset/calidad-del-aire-ozono-o3
* Acceso vía API pública de CKAN (`package\_show`), con concatenación de los 9 recursos trimestrales disponibles (2024-2026).

### Decisión metodológica: por qué O3 y no NO2

El análisis comenzó con el dataset de Dióxido de Nitrógeno (NO2), pero se descartó tras auditar la calidad de los datos: \~70% de los valores estaban vacíos, distribuidos en **bloques trimestrales completos** (varios trimestres al 100% de nulos), un patrón compatible con un problema de publicación del dataset más que con fallas normales de sensor. Se envió una consulta a `datosabiertos@imm.gub.uy` para confirmar la causa (respuesta pendiente al momento de este análisis).

Se optó por continuar con Ozono (O3), que presenta \~17% de nulos distribuidos de forma gradual por trimestre (entre 1.6% y 39.5%, sin bloques en 0% o 100%), consistente con interrupciones normales de sensor.

## Estado del proyecto

* \[x] **— Setup y descarga de datos.** Conexión a la API de CKAN, descarga y concatenación de los 9 recursos trimestrales de O3.
* \[x] **— Limpieza y preparación.** Eliminación de nulos en la columna `o3` (17.05% del total, documentado y justificado), verificación de que la limpieza no introdujo sesgo relevante entre estaciones (pérdida de 15.67% en Curva de Maroñas vs. 18.85% en Colón), y creación de columnas derivadas: `dia\_semana`, `tipo\_dia` (día de semana / fin de semana) y `hora\_de\_la\_muestra`.
* \[ ] **— Estadística descriptiva.** Media, mediana, desvío estándar por estación y por tipo de día; visualizaciones (boxplots, histograma, serie temporal). *En curso.*
* \[ ] **— Estadística inferencial.** Test de normalidad, comparación entre estaciones y entre tipo de día (t-test o Mann-Whitney según corresponda), intervalo de confianza del 95%.
* \[ ]**— Redacción del reporte final** dentro del notebook (metodología, resultados, limitaciones).
* \[ ]**— Revisión crítica** de la metodología y las conclusiones.

## Contenido del repositorio

```
├── O3.ipynb              # Notebook principal: descarga, limpieza, EDA, análisis
├── Datasets/              # CSVs crudos descargados del portal (9 trimestres, 2024-2026)
└── .gitignore
```

**Nota:** el dataset ya limpio (`Dataset\_O3\_listo.csv`) no está versionado en este repositorio por exceder el límite de tamaño de archivo de GitHub (211 MB). Se puede regenerar ejecutando el notebook de punta a punta, ya que el código de descarga y limpieza está completo y documentado.

## Cómo reproducir el análisis

1. Clonar el repositorio.
2. Instalar las dependencias: `pandas`, `requests`, `matplotlib`, `seaborn`, `scipy`.
3. Ejecutar `O3.ipynb` de principio a fin. La descarga de datos requiere conexión a internet (usa la API pública de CKAN).

## Herramientas

Python, pandas, requests (consumo de API REST), matplotlib/seaborn (visualización), scipy (estadística inferencial).

