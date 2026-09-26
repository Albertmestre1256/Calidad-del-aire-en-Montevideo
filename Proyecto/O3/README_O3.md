# Calidad del Aire en Montevideo — Ozono (O3)

> ⚠️ **Proyecto discontinuado (24/09/2026).** El análisis continúa con PM2.5, ver [../PM2.5/README.md](../PM2.5/README.md). Este notebook se conserva íntegro como evidencia del proceso metodológico.

## Pregunta de investigación

¿Existen diferencias estadísticamente significativas en los niveles de O3 entre las estaciones de monitoreo disponibles (Colón y Curva de Maroñas), y entre días de semana vs. fines de semana?

## Fuente de datos

Portal de Datos Abiertos de la Intendencia de Montevideo (CKAN), dataset de Ozono (O3) de la Red de Monitoreo de Calidad del Aire, gestionada por el Servicio de Evaluación de la Calidad y Control Ambiental (SECCA).

- Dataset: https://ckan.montevideo.gub.uy/dataset/calidad-del-aire-ozono-o3
- Acceso vía API pública de CKAN, con concatenación de los recursos trimestrales disponibles (2024-2026).

## Por qué se llegó a O3 (y no se quedó en NO2)

El análisis comenzó con Dióxido de Nitrógeno (NO2), descartado tras auditar los datos: ~70% de valores vacíos, en bloques trimestrales completos, un patrón compatible con un problema de publicación del dataset. Se pasó a Ozono (O3), con ~17% de nulos distribuidos de forma gradual, consistente con interrupciones normales de sensor.

## Por qué se discontinuó este análisis

Consultada por segunda vez la Unidad Calidad de Aire (SECCA, Intendencia de Montevideo), el Ing. Quím. Pablo Franco informó dos hallazgos que exceden lo que puede tratarse como una limitación menor:

1. **Los porcentajes reales de datos válidos son mucho más bajos de lo estimado inicialmente en este análisis.** Cifras oficiales aportadas por la Unidad: Curva de Maroñas — 77% (2023), 67% (2024), 68% (2025). Colón — 69% (2023), **37% (2024), 25% (2025)**. El ~17% de nulos calculado al inicio de este proyecto solo reflejaba valores vacíos, no la proporción real de mediciones inválidas.

2. **El dataset minutal puede contener datos inválidos mezclados con datos válidos, sin ninguna forma de detectarlo desde el análisis.** Cita textual de la respuesta recibida: *"nosotros no tenemos control sobre los datasets minutales y somos conscientes que en algún caso se cargan datos que nosotros hemos invalidado [...] no sé cuál es la falla informática pero sé que en algunas ocasiones sucede"*.

La Unidad recomendó, por segunda vez y de forma enfática, migrar a los datos horarios de la [Red de Monitoreo de la Calidad del Aire de Montevideo](https://ckan.montevideo.gub.uy/dataset/red-de-monitoreo-de-la-calidad-del-aire-de-montevideo), con más de 10 años de limpieza acumulada, y sugirió trabajar con **PM2.5** en lugar de O3, ya que ese parámetro es calibrado y mantenido directamente por la Unidad, con mayor fiabilidad del método de medición.

**Se conserva este notebook íntegro** como evidencia del proceso metodológico — la auditoría de calidad de datos, la verificación con fuentes expertas, y el hallazgo de que el dataset no era confiable para este uso son en sí mismos un resultado válido de la investigación, no un fracaso del análisis.

## Hallazgos de la etapa exploratoria (previos a la discontinuación)

### Valores atípicos: negativos y picos altos

Se detectaron 285 valores negativos de O3 (mínimo -12.0 µg/m³) y 1.002 valores por encima de 150 µg/m³ (máximo 268.0 µg/m³). Consultado el Dr. José Cataldo (Facultad de Ingeniería, UdelaR, experto en contaminación atmosférica): los negativos corresponden habitualmente a indicaciones de cero mal codificadas (se convirtieron a 0), y los valores altos son del mismo orden de magnitud que picos usuales (se conservaron en el análisis).

### Cobertura irregular de datos por estación

**Colón:** interrupciones totales confirmadas en septiembre 2024-mayo 2025 (falla de un componente electrónico, con demora en la compra del repuesto), noviembre 2025, y febrero-marzo 2024 (corte de alimentación eléctrica por obra edilicia) — las tres causas confirmadas directamente por la Unidad Calidad de Aire.

**Curva de Maroñas:** sin interrupciones totales, pero con varios meses de cobertura muy reducida.

### Estadística realizada antes de discontinuar

Test de normalidad (Shapiro-Wilk) sobre los cuatro grupos de interés confirmó distribución no normal en todos los casos. Test de Mann-Whitney entre día de semana y fin de semana arrojó una diferencia estadísticamente significativa (p-valor ≈ 0), con niveles de O3 más altos en fin de semana — resultado consistente con una hipótesis de "titulación química" (menor tráfico vehicular implica menor consumo de O3 por reacción con NO). La comparación entre estaciones quedó pendiente al momento de discontinuar el análisis.

## Cómo reproducir el análisis

1. Clonar el repositorio.
2. Instalar las dependencias: `pandas`, `requests`, `matplotlib`, `seaborn`, `scipy`.
3. Ejecutar `O3.ipynb` de principio a fin. La descarga de datos requiere conexión a internet (usa la API pública de CKAN).
