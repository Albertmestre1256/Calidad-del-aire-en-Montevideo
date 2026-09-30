# Calidad del Aire en Montevideo — PM2.5

> **Investigación finalizada (30/09/2026).** Continuación de un análisis que comenzó con NO2 y pasó por O3 — ver [../../README.md](../../README.md) para el recorrido completo, y [../O3/README_O3.md](../O3/README_O3.md) para el detalle de por qué se discontinuó O3. Quedan consultas pendientes a la Unidad Calidad de Aire (ver "Limitaciones"); si sus respuestas modifican algún resultado, se actualizará este documento.

## Pregunta de investigación

¿Existen diferencias estadísticamente significativas en los niveles de PM2.5 entre las estaciones de monitoreo disponibles, y entre días de semana vs. fines de semana?

## Fuente de datos

Dataset "Red de Monitoreo de la Calidad del Aire de Montevideo" (Portal de Datos Abiertos, CKAN), recomendado directamente por la Unidad Calidad de Aire (SECCA/IMM) por su mayor confiabilidad: datos horarios, con más de 10 años de limpieza acumulada.

- Dataset: https://ckan.montevideo.gub.uy/dataset/red-de-monitoreo-de-la-calidad-del-aire-de-montevideo
- El dataset mezcla varios contaminantes en un mismo archivo por año (2014-2025); se filtró específicamente por PM2.5 (código `PM2`).
- Los datos se descargan al ejecutar el notebook y pueden cambiar si el portal se actualiza. Fecha de la última consulta: 29/09/2026.
- Método de medición: las mediciones válidas de las tres estaciones usan un único método (`UYMVD_PM2_b`, nefelometría). Las mediciones manuales de 2007-2013 (equipo dicotómico, promedios de 24 horas) están en un archivo aparte y no se usan, por tratarse de otro método y otra escala temporal.

## Estado del proyecto

- [x] **Adquisición de datos.** Descarga y filtrado de los 12 archivos anuales del portal, quedándose solo con PM2.5. 243.184 filas iniciales.
- [x] **Limpieza y preparación.** Ver hallazgos de calidad de datos abajo. Dataset final: **3 estaciones** (Ciudad Vieja, Curva de Maroñas y Tres Cruces).
- [x] **Estadística descriptiva.** Tabla de medidas por estación y tipo de día, histogramas (escala completa y con zoom), boxplots por estación y evolución mensual por año, con los meses sin datos marcados en los gráficos.
- [x] **Estadística inferencial.** Comparación entre estaciones, de a pares (Wilcoxon, por época del año), y día de semana vs. fin de semana (Mann-Whitney), con verificaciones de robustez.
- [x] **Reporte final.** Conclusiones y limitaciones al final del notebook.
- [x] **Revisión crítica.** Verificación de supuestos, sensibilidad a las decisiones del análisis y revisión de los valores extremos.

## Unidad de análisis: la estación, no la ubicación

Según la Unidad Calidad de Aire, el identificador `station_id` (por ejemplo `UYMVD_E1`) identifica a la **estación como continuidad en el tiempo**, mientras que `ID_estacion` indica la **ubicación física** donde estuvo el monitor en cada momento. Por motivos administrativos, un mismo monitor puede mudarse de edificio:

- **Ciudad Vieja (`UYMVD_E1`):** midió en la ubicación "Ciudad Vieja 3" hasta julio de 2022 y desde entonces en "Ciudad Vieja 2".
- **Tres Cruces (`UYMVD_E5`):** el monitor se retiró de "Tres Cruces 3" a fines de 2019 y se reinstaló en "Tres Cruces 4" recién en julio de 2020.
- **Curva de Maroñas (`UYMVD_E6`):** nunca cambió de ubicación.

Por eso el análisis agrupa por `station_id`. En una primera versión se habían tratado las ubicaciones como estaciones distintas; se corrigió al recibir esta aclaración.

## Hallazgos de calidad de datos

Aun tratándose de un dataset validado y recomendado por la propia Intendencia, se encontraron varias inconsistencias durante la limpieza:

1. **Formato de archivo inconsistente en el portal.** Los recursos de 2023 y 2024 tienen el campo de formato cargado como `.CSV` (con punto) en vez de `CSV`, lo que puede excluir esos años silenciosamente si se filtra por igualdad exacta de texto. La Unidad confirmó que no tiene un motivo real y lo va a unificar.

2. **Nombres de ubicación inconsistentes entre archivos.** Dos ubicaciones de Ciudad Vieja aparecían con variantes de escritura distintas según el año (por ejemplo, con y sin espacio antes del número). Corregido y verificado cruzando con el archivo oficial de estaciones y sus coordenadas.

3. **Error en el archivo de metadatos de estaciones.** La columna de código dice "E5" cuando en los datos es "UYMVD_E5". La Unidad lo va a corregir.

4. **504 horas duplicadas exactas.** Misma estación, misma fecha y hora y el mismo valor: Tres Cruces 3 en 2019 (junio, julio y octubre) y Curva de Maroñas en febrero de 2020. Es la misma medición cargada dos veces en el archivo anual, no dos monitores midiendo a la vez. Se eliminó una copia. El traspaso de ubicación de Ciudad Vieja (julio de 2022) no presenta superposición.

5. **Un error de carga confirmado en Colón.** Los valores máximos de 2.325 y 1.575 µg/m³ quedaron multiplicados por 1000 por error; según la Unidad, debieron ser 2 µg/m³.

6. **Una estación excluida:** **Colón** tiene registros solo en 2017 y 2018 (unas 9.700 mediciones válidas), sin superposición con el período de Tres Cruces, que empieza en 2019. Es un volumen y un período insuficientes para compararla con las demás.

7. **Meses sin datos, o con muy pocos, en algunas estaciones y años:**
   - Curva de Maroñas: febrero a junio de 2021 y febrero a julio de 2022, sin mediciones.
   - Ciudad Vieja: junio de 2020, sin mediciones.
   - Tres Cruces: primer semestre de 2020 (la mudanza del monitor) y desde junio de 2025.

   Se detectaron al notar tramos "demasiado rectos" en los gráficos de evolución mensual: un gráfico de líneas une los meses con datos sin avisar del hueco intermedio. Ahora los meses vacíos quedan como huecos en la línea y se marcan en rojo.

8. **Cobertura mínima.** Un promedio mensual solo cuenta si el sensor midió al menos el 75% de las horas del mes (convención habitual en calidad del aire). Esto excluye 19 meses de los gráficos mensuales. Varios de ellos son junios, es decir, pleno invierno, la época de mayor PM2.5. Si los datos faltan sobre todo en los meses más contaminados, los promedios pueden subestimar los niveles de invierno.

9. **Un código de método que no figura en los metadatos.** Antes de eliminar los nulos, Curva de Maroñas tenía 168 filas con el código `UYMVD_PM2b_2`. Ninguna tiene valor medido, por lo que desaparecen al eliminar los nulos y no afectan al análisis. Su significado queda pendiente de consultar a la Unidad.

Los hallazgos se reportan a la Unidad Calidad de Aire, como parte del mismo proceso de verificación aplicado anteriormente a NO2 y O3.

## Resultados

### Metodología

Los tests se hicieron sobre **promedios diarios** y no sobre las mediciones horarias, porque horas consecutivas se parecen mucho entre sí y los tests asumen observaciones independientes. Un día solo cuenta si tiene al menos 18 de las 24 horas.

### Comparación entre estaciones (Wilcoxon, por época del año)

Se compararon las estaciones de a pares (Curva de Maroñas contra Ciudad Vieja, Tres Cruces contra Ciudad Vieja, y Curva de Maroñas contra Tres Cruces), usando solo los 1.404 días en que las tres estaciones tienen dato válido (17/05/2019 al 28/05/2025), de modo que cada día se compara consigo mismo. Como la diferencia entre estaciones cambia según la época del año, se analizó cada época por separado.

| Época | Maroñas − Ciudad Vieja (mediana) | Tres Cruces − Ciudad Vieja (mediana) | Maroñas − Tres Cruces (mediana) |
|---|---|---|---|
| Verano | 1,6 µg/m³ | 1,0 µg/m³ | 0,6 µg/m³ |
| Otoño | 2,4 µg/m³ | 0,8 µg/m³ | 1,7 µg/m³ |
| Invierno | 4,7 µg/m³ | 2,8 µg/m³ | 1,8 µg/m³ |
| Primavera | 2,1 µg/m³ | 1,0 µg/m³ | 0,9 µg/m³ |

Las doce comparaciones (tres pares en cuatro épocas) son estadísticamente significativas, incluso con la corrección de Bonferroni (umbral de 0,05 / 12 = 0,0042). El orden es el mismo en las cuatro épocas: Curva de Maroñas registra más PM2.5 que Tres Cruces, y Tres Cruces más que Ciudad Vieja. La diferencia con Ciudad Vieja es mayor en invierno.

### Día de semana vs. fin de semana (Mann-Whitney, por estación)

| Estación | Diferencia de medianas (fin de semana − semana) | p-valor |
|---|---|---|
| Ciudad Vieja | −0,1 µg/m³ | 0,40 |
| Curva de Maroñas | +1,1 µg/m³ | 0,0014 |
| Tres Cruces | −0,2 µg/m³ | 0,40 |

En Ciudad Vieja y Tres Cruces no se detectó diferencia entre día de semana y fin de semana. En Curva de Maroñas el fin de semana es 1,1 µg/m³ más alto, una diferencia que supera el umbral corregido (0,05 / 3 = 0,0167) pero es pequeña y conviene confirmar con más cuidado.

**Corrección de una lectura anterior.** Los boxplots horarios sugerían valores más altos entre semana en Ciudad Vieja, y una versión anterior de este README lo interpretaba como efecto del tráfico. El test no lo confirma: probablemente era un efecto del redondeo a enteros de los datos publicados. Por eso esa hipótesis no se sostiene con estos datos.

### Verificaciones de robustez

- **Sensibilidad al umbral de horas por día.** Se repitió la comparación entre estaciones exigiendo 12 y 20 horas en lugar de 18. Los días en común pasan de 1.404 a 1.433 y 1.402, las medianas casi no cambian (la única diferencia es de una décima), las doce comparaciones siguen siendo significativas y el orden entre estaciones se mantiene.
- **Supuesto de simetría del test de Wilcoxon.** El test asume que las diferencias diarias son simétricas alrededor de su mediana. Se verificó con histogramas y coeficientes de asimetría, y **no se cumple en la mayoría de los casos** (el coeficiente supera 1 en valor absoluto en 10 de 12 combinaciones). Las medianas siguen siendo un resumen válido, pero los p-valores del Wilcoxon se leen con reserva.
- **Test de signos.** Como no requiere simetría, se repitieron con él las doce comparaciones: todas siguen siendo significativas. Según el par y la época, la primera estación superó a la segunda en entre el 63% y el 85% de los días. El par con menos margen es Curva de Maroñas − Tres Cruces. Por lo tanto, el orden entre estaciones no depende del supuesto de simetría.

### Valores extremos

Las mayores diferencias diarias entre estaciones (de ±60 a ±81 µg/m³) corresponden a episodios de varias horas seguidas, de madrugada, en una sola estación (17/05/2023 y 20/03/2025, ambos en Curva de Maroñas) o en varias a la vez (03/05/2025, con Ciudad Vieja como la más alta). En el conjunto de los datos hay 73 valores horarios de 300 µg/m³ o más; 68 de ellos (93%) ocurren entre las 20 y las 4 hs, y ninguno entre las 12 y las 19 hs. Esto contrasta con el ozono, cuyos valores altos se concentraban al mediodía.

**Su origen no se pudo determinar.** Caben al menos tres explicaciones: una fuente local nocturna, un efecto de la humedad o la niebla sobre el sensor (el método es nefelometría), o un problema del equipo. Las medianas y los tests por rangos son poco sensibles a unos pocos días extremos; las medias sí lo son.

## Conclusiones

1. **Entre estaciones.** El orden es el mismo en las cuatro épocas del año: Curva de Maroñas registra más PM2.5 que Tres Cruces, y Tres Cruces más que Ciudad Vieja. La diferencia mediana diaria respecto de Ciudad Vieja va de 1,6 a 4,7 µg/m³ en Curva de Maroñas y de 0,8 a 2,8 µg/m³ en Tres Cruces, y es mayor en invierno. El resultado no depende del umbral de horas por día ni del supuesto de simetría.
2. **Día de semana frente a fin de semana.** No se detectó diferencia en Ciudad Vieja ni en Tres Cruces. En Curva de Maroñas el fin de semana es 1,1 µg/m³ más alto: un resultado sugerente, no concluyente.
3. **Alcance.** Son tres puntos de medición durante los días con dato válido. Los resultados no permiten hablar de zonas enteras de la ciudad, explicar por qué difieren las estaciones, concluir que "no hay diferencia" donde no se detectó ninguna, ni evaluar efectos en la salud o el cumplimiento de una norma.

## Limitaciones

- **Sesgo de selección.** El conjunto de días en común deja pocos días de junio (35 de unos 180 posibles), el mes de mayor PM2.5, así que los resultados de invierno se apoyan sobre todo en julio y agosto. El nivel general de este subconjunto es menor al real, aunque la diferencia entre estaciones, que se calcula día por día, se ve mucho menos afectada.
- **Los p-valores son orientativos.** Días consecutivos se parecen entre sí, lo que hace que salgan más bajos de lo que corresponde, tanto en el Wilcoxon como en el test de signos. Por eso se reportan como p < 0,001.
- **Significativo no significa grande.** Con cientos de días por grupo, los tests detectan diferencias muy pequeñas (la menor es de 0,6 µg/m³), y los datos publicados están redondeados a enteros.
- **No se puede atribuir una causa.** Las diferencias entre estaciones mezclan el lugar, el equipo y los cambios de ubicación de los monitores en Ciudad Vieja y Tres Cruces.
- **Zona horaria desconocida.** Los metadatos no aclaran si `date` está en hora de Uruguay o en UTC. Si fuera UTC, algunas horas de la noche quedarían asignadas al día vecino y algunas mediciones caerían en el grupo equivocado entre semana y fin de semana.
- **Picos y días extremos sin explicar** (ver arriba).
- **Colón excluida** por tener datos solo en 2017 y 2018.

### Consultas pendientes a la Unidad Calidad de Aire

La zona horaria de `date`, el motivo de los meses sin datos, la menor cobertura de junio, la validez de los picos y el efecto de la humedad sobre la nefelometría, y el significado del código `UYMVD_PM2b_2`.

## Cómo reproducir el análisis

1. Clonar el repositorio.
2. Instalar las dependencias: `pandas`, `requests`, `matplotlib`, `seaborn`, `scipy`, `pyproj`, `folium`.
3. Ejecutar el notebook de principio a fin. La descarga de datos requiere conexión a internet (usa la API pública de CKAN). Como el portal se actualiza, los números pueden diferir levemente de los de este documento si se ejecuta en otra fecha.
