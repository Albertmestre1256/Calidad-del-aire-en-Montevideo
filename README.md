# Calidad del Aire en Montevideo — Análisis Estadístico

Proyecto personal de estadística aplicada, usando datos abiertos de la Intendencia de Montevideo, como parte de un proceso de fortalecimiento de base cuantitativa en ciencia de datos.

> **Estado: investigación finalizada (30/09/2026).** El análisis con PM2.5 está completo. Quedan consultas pendientes a la Unidad Calidad de Aire de la Intendencia (ver [Proyecto/PM2.5/README_PM25.md](Proyecto/PM2.5/README_PM25.md)); si sus respuestas modifican algún resultado, se actualizará.

## Recorrido del proyecto

Este proyecto cambió de contaminante dos veces, cada vez por hallazgos de calidad de datos confirmados con la propia Intendencia de Montevideo — no por decisión arbitraria. Resumen breve:

1. **NO2** → descartado: ~70% de valores faltantes, en bloques trimestrales completos.
2. **O3** → descartado: consultada la Unidad Calidad de Aire (SECCA/IMM), se confirmó que el dataset minutal puede contener datos inválidos mezclados con válidos sin ninguna señal detectable, y que los porcentajes reales de datos válidos eran mucho más bajos de lo estimado (hasta 25% en una estación). Ver [Proyecto/O3/README_O3.md](Proyecto/O3/README_O3.md) para el detalle completo.
3. **PM2.5** → análisis final. Se migró al dataset horario de la Red de Monitoreo, recomendado por la propia Intendencia por tener más de 10 años de limpieza validada, y a PM2.5 por ser un parámetro calibrado y mantenido directamente por la Unidad, sin tercerización. Ver [Proyecto/PM2.5/README_PM25.md](Proyecto/PM2.5/README_PM25.md) para el detalle y los resultados.

## Resumen del análisis con PM2.5

- **Datos:** 243.184 filas de PM2.5 (2015-2025). Dataset de trabajo final: **3 estaciones** (Ciudad Vieja, Curva de Maroñas y Tres Cruces). La Unidad Calidad de Aire aclaró que un mismo monitor puede cambiar de ubicación física con el tiempo, por lo que el análisis agrupa por estación (`station_id`) y no por ubicación. Solo Colón quedó excluida, por tener datos únicamente en 2017-2018.
- **Calidad de datos.** Se identificaron y reportaron a la Unidad: una inconsistencia de formato de archivo en el portal (2023-2024 cargados como `.CSV`), nombres de ubicación inconsistentes entre archivos, un error en el archivo de metadatos de estaciones, 504 horas duplicadas exactas, meses enteros sin mediciones en algunas estaciones, un código de método que no figura en los metadatos y un error de carga en Colón (valores multiplicados por 1000, confirmado por la Unidad).
- **Estadística descriptiva:** tabla de medidas por estación y tipo de día, histogramas, boxplots y evolución mensual por año, con los meses sin datos marcados en los gráficos. Se generó también un mapa interactivo con la ubicación real de los monitores.
- **Entre estaciones.** Sobre promedios diarios, el orden es el mismo en las cuatro épocas del año: Curva de Maroñas registra más PM2.5 que Tres Cruces, y Tres Cruces más que Ciudad Vieja, con la mayor brecha respecto a Ciudad Vieja en invierno.
- **Día de semana vs. fin de semana.** No se detectó diferencia en Ciudad Vieja ni en Tres Cruces; en Curva de Maroñas el fin de semana es levemente más alto (1,1 µg/m³). Una lectura inicial de los gráficos sugería valores más altos entre semana en Ciudad Vieja; el test no la confirmó y se descartó.
- **Verificaciones de robustez.** El resultado entre estaciones no cambia al exigir 12 o 20 horas por día en lugar de 18. El supuesto de simetría del test principal no se cumple, y un test que no lo requiere (test de signos) confirma las mismas conclusiones.
- **Valores extremos.** Los valores horarios de 300 µg/m³ o más se concentran de noche y madrugada (93% entre las 20 y las 4 hs). Su origen no se pudo determinar.
- **Cómo leer los resultados:** son orientativos. Los datos publicados están redondeados a enteros, días consecutivos se parecen entre sí, y hay consultas pendientes a la Unidad (entre ellas, la zona horaria de las fechas).

Detalle completo en [Proyecto/PM2.5/README_PM25.md](Proyecto/PM2.5/README_PM25.md).

## Estructura del repositorio

```
├── Datasets/
│   ├── O3/                          # CSVs crudos usados en la etapa de O3
│   └── PM2.5/                        # CSVs crudos usados en la etapa de PM2.5
├── Proyecto/
│   ├── O3/
│   │   ├── O3 - descontinuado.ipynb  # Notebook completo: descarga, limpieza, EDA, inferencia
│   │   └── README_O3.md              # Qué se hizo y por qué se discontinuó
│   └── PM2.5/
│       ├── PM2_5_-_Análisis.ipynb    # Análisis completo y conclusiones
│       └── README_PM25.md            # Metodología, resultados, conclusiones y limitaciones
```

## Herramientas

Python, pandas, requests (consumo de API REST de CKAN), matplotlib/seaborn (visualización), scipy (estadística inferencial), pyproj/folium (visualización geoespacial).
