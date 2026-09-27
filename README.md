# Calidad del Aire en Montevideo — Análisis Estadístico

Proyecto personal de estadística aplicada, usando datos abiertos de la Intendencia de Montevideo, como parte de un proceso de fortalecimiento de base cuantitativa en ciencia de datos.

## Recorrido del proyecto

Este proyecto cambió de contaminante dos veces, cada vez por hallazgos de calidad de datos confirmados con la propia Intendencia de Montevideo — no por decisión arbitraria. Resumen breve:

1. **NO2** → descartado: ~70% de valores faltantes, en bloques trimestrales completos.
2. **O3** → descartado: consultada la Unidad Calidad de Aire (SECCA/IMM), se confirmó que el dataset minutal puede contener datos inválidos mezclados con válidos sin ninguna señal detectable, y que los porcentajes reales de datos válidos eran mucho más bajos de lo estimado (hasta 25% en una estación). Ver [Proyecto/O3/README_O3.md](Proyecto/O3/README_O3.md) para el detalle completo.
3. **PM2.5** → proyecto actual. Se migró al dataset horario de la Red de Monitoreo, recomendado por la propia Intendencia por tener más de 10 años de limpieza validada, y a PM2.5 por ser un parámetro calibrado y mantenido directamente por la Unidad, sin tercerización. Ver [Proyecto/PM2.5/README_PM25.md](Proyecto/PM2.5/README_PM25.md) para el estado actual.

## Avance actual (PM2.5)

- **Adquisición de datos completa:** 243.184 filas de PM2.5 descargadas (2015-2025). Dataset de trabajo final, tras limpieza: **4 estaciones** (Ciudad Vieja 2, Ciudad Vieja 3, Curva de Maronas, Tres Cruces 4).
- **Limpieza completa**, con varios hallazgos de calidad de datos identificados y corregidos: inconsistencia de formato de archivo en el portal (2023-2024 cargados como `.CSV`), nombres de estación inconsistentes entre archivos (corregidos y verificados con las coordenadas geográficas oficiales), y dos estaciones excluidas por aportar muy poco volumen de datos útiles (Tres Cruces 3, con un año completo inválido, y Colón, con solo dos años de vida útil).
- Se generó un mapa interactivo con la ubicación real de las estaciones, como parte de la verificación de nombres.
- **Estadística descriptiva completa:** tabla de medidas por estación y tipo de día, histogramas, boxplots por estación (con detalle año a año) y boxplot general combinado. Se detectó un patrón interesante: en algunas estaciones, los niveles de PM2.5 son más altos en días de semana que en fines de semana — lo opuesto al patrón encontrado con O3 —, consistente con que PM2.5 es un contaminante emitido directamente por el tráfico, a diferencia de O3, que se forma por reacción química.
- Todos los hallazgos de calidad de datos se están reportando a la Unidad Calidad de Aire, como parte del mismo proceso de verificación aplicado a NO2 y O3.

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
