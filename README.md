# Calidad del Aire en Montevideo — Análisis Estadístico

Análisis exploratorio e inferencial sobre los niveles de Ozono (O3) en Montevideo, utilizando datos abiertos publicados por la Intendencia de Montevideo. Proyecto personal, realizado como parte de un proceso de fortalecimiento de base cuantitativa en ciencia de datos.

> ⚠️ **Proyecto discontinuado (24/09/2026) — ver aclaración abajo.** El análisis continúa con el parámetro PM2.5, usando una fuente de datos validada, en un repositorio nuevo: *(pendiente agregar link cuando exista)*.

## Por qué se discontinuó este análisis

Consultada por segunda vez la Unidad Calidad de Aire (SECCA, Intendencia de Montevideo), el Ing. Quím. Pablo Franco informó dos hallazgos que exceden lo que puede tratarse como una limitación menor documentable:

1. **Los porcentajes reales de datos válidos son mucho más bajos de lo estimado inicialmente en este análisis.** Cifras oficiales aportadas por la Unidad: Curva de Maroñas — 77% (2023), 67% (2024), 68% (2025). Colón — 69% (2023), **37% (2024), 25% (2025)**. El ~17% de nulos calculado al inicio de este proyecto solo reflejaba valores vacíos, no la proporción real de mediciones inválidas.

2. **El dataset minutal puede contener datos inválidos mezclados con datos válidos, sin ninguna forma de detectarlo desde el análisis.** Cita textual de la respuesta recibida: *"nosotros no tenemos control sobre los datasets minutales y somos conscientes que en algún caso se cargan datos que nosotros hemos invalidado [...] no sé cuál es la falla informática pero sé que en algunas ocasiones sucede"*.

La Unidad recomendó, por segunda vez y de forma enfática, migrar a los datos horarios de la [Red de Monitoreo de la Calidad del Aire de Montevideo](https://ckan.montevideo.gub.uy/dataset/red-de-monitoreo-de-la-calidad-del-aire-de-montevideo), con más de 10 años de limpieza acumulada, y sugirió trabajar con **PM2.5** en lugar de O3, ya que ese parámetro es calibrado y mantenido directamente por la Unidad (sin tercerización), con mayor fiabilidad del método de medición.

**Se conserva este repositorio íntegro** como evidencia del proceso metodológico — la auditoría de calidad de datos, la verificación con fuentes expertas, y el hallazgo de que el dataset no era confiable para este uso son en sí mismos un resultado válido de la investigación, no un fracaso del análisis.

## Pregunta de investigación

¿Existen diferencias estadísticamente significativas en los niveles de O3 entre las dos estaciones de monitoreo disponibles (Colón y Curva de Maroñas), y entre días de semana vs. fines de semana?

## Fuente de datos

Portal de Datos Abiertos de la Intendencia de Montevideo (CKAN), dataset de Ozono (O3) de la Red de Monitoreo de Calidad del Aire, gestionada por el Servicio de Evaluación de la Calidad y Control Ambiental (SECCA).

- Dataset: https://ckan.montevideo.gub.uy/dataset/calidad-del-aire-ozono-o3
- Acceso vía API pública de CKAN (`package_show`), con concatenación de los 9 recursos trimestrales disponibles (2024-2026).

### Decisión metodológica: por qué O3 y no NO2

El análisis comenzó con el dataset de Dióxido de Nitrógeno (NO2), pero se descartó tras auditar la calidad de los datos: ~70% de los valores estaban vacíos, distribuidos en **bloques trimestrales completos** (varios trimestres al 100% de nulos), un patrón compatible con un problema de publicación del dataset más que con fallas normales de sensor.

Se optó por continuar con Ozono (O3), que presenta ~17% de nulos distribuidos de forma gradual por trimestre (entre 1.6% y 39.5%, sin bloques en 0% o 100%), consistente con interrupciones normales de sensor.

**Confirmación institucional (23/09/2026):** consultado el equipo de la Unidad Calidad de Aire (SECCA/IMM) sobre los faltantes de NO2, el Ing. Quím. Pablo Franco confirmó que ese conjunto de datos presenta indicios de mezcla entre valores válidos e inválidos, y explicó la causa general de los faltantes en las mediciones de calidad del aire: el método de medición emplea un semiconductor sensible al gas, con menor fiabilidad que los métodos de referencia, y el proveedor de calibraciones ha tenido fallas recurrentes en los últimos años, llevando a la invalidación de una gran cantidad de medidas. Se recomendó también una fuente alternativa de datos horarios ya validados; se optó por continuar con el dataset minutal actual dado el plazo del proyecto y el avance ya realizado sobre él.

## Hallazgos de la etapa exploratoria

### Valores atípicos: negativos y picos altos

Se detectaron 285 valores negativos de O3 (mínimo -12.0 µg/m³) y 1.002 valores por encima de 150 µg/m³ (máximo 268.0 µg/m³). Consultado el Dr. José Cataldo (Facultad de Ingeniería, UdelaR, experto en contaminación atmosférica), se confirmó que:

- Los valores negativos corresponden habitualmente a indicaciones de cero mal codificadas → se convirtieron a 0 en lugar de eliminarse, ya que representan mediciones reales de concentración nula.
- Los valores altos (~260 µg/m³), aunque elevados, son del mismo orden de magnitud que picos usuales (120-140 µg/m³) → se conservaron en el análisis, sin excluirlos como outliers. Su distribución horaria se concentra entre las 12 y las 18hs, consistente con el ciclo fotoquímico del ozono troposférico.

### Cobertura irregular de datos por estación

La investigación reveló patrones distintos de datos faltantes en cada estación, verificados contra el dataset original (sin nulos eliminados) para descartar errores propios de procesamiento:

- **Colón** presenta interrupciones totales (sin ningún registro, ni siquiera nulo) en dos períodos: **septiembre 2024 a mayo 2025**, y **noviembre 2025**.
- **Curva de Maroñas** no presenta interrupciones totales, pero sí varios meses de cobertura muy reducida, con dos mecanismos distintos identificados: meses con pocas mediciones generadas en general (nov. 2024, ago. 2026) y meses con mediciones normales en cantidad pero mayoritariamente inválidas (jun. y sep. 2025, jun. y sep. 2026 — hasta 84% de valores nulos en el peor caso).

Esta irregularidad es consistente con la explicación institucional recibida sobre fallas recurrentes de calibración. **Decisión metodológica:** no se comparan ambas estaciones en los períodos donde una carece de datos; los gráficos de evolución anual muestran cada año por separado, con las zonas sin datos marcadas visualmente.

### Precisión sobre los gráficos de promedio

Los gráficos de perfil mensual muestran promedios, que por diseño diluyen los valores extremos entre miles de mediciones normales — no deben interpretarse como ausencia de picos altos. Para el análisis de valores extremos, ver la tabla de picos por hora en el notebook. Asimismo, el año 2026 se encuentra incompleto en el dataset (datos hasta septiembre), por lo que los promedios anuales de ese año no son comparables en igualdad de condiciones con 2024 y 2025.

## Estado del proyecto

- [x] **Adquisición de datos.** Conexión a la API de CKAN, descarga y concatenación de los 9 recursos trimestrales de O3.
- [x] **Limpieza y preparación.** Eliminación de nulos en la columna `o3` (17.05% del total, documentado y justificado), tratamiento de valores atípicos (ver arriba), verificación de que la limpieza no introdujo sesgo relevante entre estaciones (pérdida de 15.67% en Curva de Maroñas vs. 18.85% en Colón), y creación de columnas derivadas: `dia_semana`, `tipo_dia` (día de semana / fin de semana), `hora_de_la_muestra` y `mes_numero`.
- [x] **Estadística descriptiva.** Media, mediana, desvío estándar por estación y por tipo de día; visualizaciones (boxplots generales y por estación, histograma, perfil horario, perfil mensual por año con estaciones combinadas).
- [ ] **Estadística inferencial.** Test de normalidad, comparación entre estaciones y entre tipo de día (t-test o Mann-Whitney según corresponda), intervalo de confianza del 95%.
- [ ] **Redacción del reporte final** dentro del notebook (metodología, resultados, limitaciones).
- [ ] **Revisión crítica** de la metodología y las conclusiones.

## Contenido del repositorio

```
├── O3.ipynb              # Notebook principal: descarga, limpieza, EDA, análisis
├── Datasets/              # CSVs crudos descargados del portal (9 trimestres, 2024-2026)
└── .gitignore
```

**Nota:** el dataset ya limpio (`Dataset_O3_listo.csv`) no está versionado en este repositorio por exceder el límite de tamaño de archivo de GitHub (211 MB). Se puede regenerar ejecutando el notebook de punta a punta, ya que el código de descarga y limpieza está completo y documentado.

## Cómo reproducir el análisis

1. Clonar el repositorio.
2. Instalar las dependencias: `pandas`, `requests`, `matplotlib`, `seaborn`, `scipy`.
3. Ejecutar `O3.ipynb` de principio a fin. La descarga de datos requiere conexión a internet (usa la API pública de CKAN).

## Herramientas

Python, pandas, requests (consumo de API REST), matplotlib/seaborn (visualización), scipy (estadística inferencial).
