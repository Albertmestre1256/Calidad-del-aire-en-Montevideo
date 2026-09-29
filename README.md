# Calidad del Aire en Montevideo — Análisis Estadístico

Proyecto personal de estadística aplicada, usando datos abiertos de la Intendencia de Montevideo, como parte de un proceso de fortalecimiento de base cuantitativa en ciencia de datos.

## Recorrido del proyecto

Este proyecto cambió de contaminante dos veces, cada vez por hallazgos de calidad de datos confirmados con la propia Intendencia de Montevideo — no por decisión arbitraria. Resumen breve:

1. **NO2** → descartado: ~70% de valores faltantes, en bloques trimestrales completos.
2. **O3** → descartado: consultada la Unidad Calidad de Aire (SECCA/IMM), se confirmó que el dataset minutal puede contener datos inválidos mezclados con válidos sin ninguna señal detectable, y que los porcentajes reales de datos válidos eran mucho más bajos de lo estimado (hasta 25% en una estación). Ver [Proyecto/O3/README_O3.md](Proyecto/O3/README_O3.md) para el detalle completo.
3. **PM2.5** → proyecto actual. Se migró al dataset horario de la Red de Monitoreo, recomendado por la propia Intendencia por tener más de 10 años de limpieza validada, y a PM2.5 por ser un parámetro calibrado y mantenido directamente por la Unidad, sin tercerización. Ver [Proyecto/PM2.5/README_PM25.md](Proyecto/PM2.5/README_PM25.md) para el estado actual.

## Avance actual (PM2.5)

- **Adquisición de datos completa:** 243.184 filas de PM2.5 descargadas (2015-2025).
- **Limpieza completa.** Dataset de trabajo final: **3 estaciones** (Ciudad Vieja, Curva de Maroñas y Tres Cruces). La Unidad Calidad de Aire aclaró que un mismo monitor puede cambiar de ubicación física con el tiempo, por lo que el análisis agrupa por estación (`station_id`) y no por ubicación. Solo Colón quedó excluida, por tener datos únicamente en 2017-2018.
- **Hallazgos de calidad de datos identificados y reportados a la Unidad:** una inconsistencia de formato de archivo en el portal (2023-2024 cargados como `.CSV`), nombres de ubicación inconsistentes entre archivos, 504 horas duplicadas exactas, meses enteros sin mediciones en algunas estaciones, y un error de carga en Colón (valores multiplicados por 1000, confirmado por la Unidad).
- Se generó un mapa interactivo con la ubicación real de los monitores, como parte de la verificación de nombres.
- **Estadística descriptiva completa:** tabla de medidas por estación y tipo de día, histogramas, boxplots y evolución mensual por año, con los meses sin datos marcados en los gráficos.
- **Estadística inferencial en curso.** Sobre promedios diarios: Curva de Maroñas y Tres Cruces registran más PM2.5 que Ciudad Vieja en las cuatro épocas del año, con la mayor diferencia en invierno. Entre días de semana y fines de semana no se detectó diferencia en Ciudad Vieja ni en Tres Cruces, y en Curva de Maroñas el fin de semana es levemente más alto (1,1 µg/m³). Una lectura inicial de los gráficos sugería valores más altos entre semana en Ciudad Vieja; el test no la confirmó y se descartó.
- Los resultados son orientativos: los datos publicados están redondeados a enteros, días consecutivos se parecen entre sí, y hay consultas pendientes a la Unidad (entre ellas, la zona horaria de las fechas).

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
│       ├── PM2_5_-_Análisis.ipynb
│       └── README_PM25.md            # Estado actual del proyecto en curso
```

## Herramientas

Python, pandas, requests (consumo de API REST de CKAN), matplotlib/seaborn (visualización), scipy (estadística inferencial), pyproj/folium (visualización geoespacial).
