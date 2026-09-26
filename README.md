# Calidad del Aire en Montevideo — Análisis Estadístico

Proyecto personal de estadística aplicada, usando datos abiertos de la Intendencia de Montevideo, como parte de un proceso de fortalecimiento de base cuantitativa en ciencia de datos.

## Recorrido del proyecto

Este proyecto cambió de contaminante dos veces, cada vez por hallazgos de calidad de datos confirmados con la propia Intendencia de Montevideo — no por decisión arbitraria. Resumen breve:

1. **NO2** → descartado: ~70% de valores faltantes, en bloques trimestrales completos.
2. **O3** → descartado: consultada la Unidad Calidad de Aire (SECCA/IMM), se confirmó que el dataset minutal puede contener datos inválidos mezclados con válidos sin ninguna señal detectable, y que los porcentajes reales de datos válidos eran mucho más bajos de lo estimado (hasta 25% en una estación). Ver [Calidad-del-aire-en-Montevideo/Proyecto/O3/README_O3.md](/Proyecto/O3/README_O3.md) para el detalle completo.
3. **PM2.5** → proyecto actual. Se migró al dataset horario de la Red de Monitoreo, recomendado por la propia Intendencia por tener más de 10 años de limpieza validada, y a PM2.5 por ser un parámetro calibrado y mantenido directamente por la Unidad, sin tercerización. Ver [Proyecto/Proyecto - PM2.5/README.md](Proyecto/PM2.5/README.md) para el estado actual.

## Avance actual (PM2.5)

- **Adquisición de datos completa:** 243.184 filas de PM2.5, correspondientes al período 2015-2025 (2014 no tiene mediciones de este contaminante).
- **Limpieza en curso**, con dos hallazgos de calidad de datos ya identificados y corregidos: una inconsistencia de formato de archivo en el portal (recursos de 2023-2024 cargados como `.CSV` en vez de `CSV`, lo que puede excluirlos silenciosamente de un filtro), y nombres de estación inconsistentes entre archivos (una misma estación con dos variantes de escritura), verificado y corregido cruzando con las coordenadas geográficas de cada estación.
- Se generó un mapa interactivo con la ubicación real de las 6 estaciones que miden PM2.5, como parte de la verificación anterior.
- Ambos hallazgos se están reportando a la Unidad Calidad de Aire, como parte del mismo proceso de verificación aplicado a NO2 y O3.

Detalle completo en [Proyecto/PM2.5/README.md](Proyecto/PM2.5/README.md).

## Estructura del repositorio

```
├── Datasets/
│   ├── O3/              # CSVs crudos usados en la etapa de O3
│   └── PM2.5/            # CSVs crudos usados en la etapa de PM2.5
├── Proyecto/
│   ├── O3/
│   │   ├── O3.ipynb      # Notebook completo: descarga, limpieza, EDA, inferencia
│   │   └── README.md     # Qué se hizo y por qué se discontinuó
│   └── PM2.5/
│       ├── PM2_5_-_Análisis.ipynb
│       └── README.md     # Estado actual del proyecto en curso
```

## Herramientas

Python, pandas, requests (consumo de API REST de CKAN), matplotlib/seaborn (visualización), scipy (estadística inferencial), pyproj/folium (visualización geoespacial).
