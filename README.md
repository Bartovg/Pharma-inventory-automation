# Automatización del control de inventarios farmacéuticos multi-sede

**Caso de estudio · Business Analytics · Google Apps Script + Looker Studio**

📊 **Dashboard interactivo:** [Ver en Looker Studio](https://datastudio.google.com/s/nRbwY9k_qSE) *(construido con datos ficticios que replican la estructura del sistema real)*

**En resumen:** convertí un proceso manual de 12-15 horas semanales en un flujo automatizado de 30-40 minutos, que en 3 meses identificó **160-190 millones de pesos en inventario en riesgo de vencimiento** y contribuyó a reducir casi un 50% la pérdida mensual por vencimiento. Hoy genera órdenes de traslado accionables entre sedes.

\---

## 1\. Contexto

Soy Referente y Analista de Inventarios en una IPS de salud. Lidero el control de inventario farmacéutico de una red de **18 puntos**: 15 sedes de dispensación, 1 bodega central y 2 puntos de alto costo. Trabajo con 3 auxiliares, cada uno a cargo de un grupo de sedes asignado según tamaño y complejidad operativa (no de forma equitativa por cantidad).

## 2\. El problema

Cada semana había que revisar manualmente tres frentes por cada sede:

* Pendientes de entrega a pacientes.
* Disponibilidad de inventario próximo a vencer.
* Posibles traslados de stock entre sedes para evitar pérdidas.

Buscar en archivos, cruzar información y redactar correos tomaba **12-15 horas semanales** (1.5 a 2 jornadas completas). Pero el costo real no era el tiempo: era la **pérdida económica por medicamentos que vencían** porque nadie tenía visibilidad centralizada para detectarlos y trasladarlos a tiempo.

## 3\. Cómo entendí el problema

* **Definí el objetivo de negocio antes de construir:** evitar vencimientos, por encima de resolver pendientes. Esa decisión ordena todo el diseño: un artículo en riesgo se prioriza aunque no tenga demanda inmediata.
* **Identifiqué la necesidad por iniciativa propia.** En control de inventario farmacéutico evitar vencimientos es vital, y vi dos señales: se acumulaban muchos pendientes y cada mes vencían muchas moléculas. Ya existía un ejercicio manual (enviar a la bodega las moléculas próximas a vencer para buscarles salida), pero se hacía solo con la base de datos del sistema de inventarios, y por eso consumía 12-15 horas semanales.
* **Qué debía resolver el sistema:** evitar vencimientos, cubrir pendientes reales, refrescar el stock en las sedes y detectar el riesgo de pérdida real.
* **Validación:** los resultados se comprobaron casi de inmediato. Las sugerencias de traslado empezaron a cubrir pendientes reales y el valor de pérdida mensual por vencimiento disminuyó casi un 50%.

## 4\. La solución

Un sistema en **Google Apps Script** que:

* **Consolida automáticamente** dispensación, pendientes e inventario de las 18 sedes desde los archivos fuente, sin intervención manual.
* **Clasifica el riesgo de vencimiento** por artículo y sede en cuatro niveles: vencido, crítico, alto y medio.
* **Cruza pendientes con disponibilidad próxima a vencer** y genera órdenes de traslado entre sedes, cuidando primero el **stock de seguridad** de la sede de origen. Los auxiliares envían las órdenes por correo y cada sede debe responder con evidencia del traslado.
* **Detecta inventario sin movimiento:** artículos próximos a vencer sin dispensación reciente ni pendientes que los consuman. Es el segmento de mayor riesgo real, porque no tienen ninguna vía natural para rotar antes de vencerse.

## 5\. Métricas y KPIs

|Indicador|Qué mide|Para qué se usa|
|-|-|-|
|Valor en riesgo de vencimiento|Valor monetario del inventario clasificado como vencido, crítico, alto o medio|Priorizar dónde actuar primero|
|Nivel de riesgo|Clasificación por severidad según cercanía al vencimiento: Vencido = 0 días, Crítico = 30 días, Alto = 60 días, Medio = 90 o más días.|Ordenar la atención de los auxiliares|
|Sugerencias de traslado por sede|Más de 10 por sede cada semana|Convertir el riesgo en acciones concretas|
|Stock de seguridad de origen|Unidades mínimas que la sede de origen conserva antes de trasladar|Evitar resolver un riesgo creando un desabastecimiento|
|Artículos sin rotación|Próximos a vencer sin dispensación ni pendientes|Identificar el mayor riesgo de pérdida|
|Tiempo de gestión semanal|12-15 h antes, 30-40 min ahora|Medir la eficiencia del proceso|
|Pérdida mensual por vencimiento|Valor monetario de los medicamentos que vencen cada mes|Medir el impacto real: bajó casi un 50%|

## 6\. Dashboard

El dashboard en [Looker Studio](https://datastudio.google.com/s/nRbwY9k_qSE) presenta la misma estructura de información que usa el sistema real: dispensas, pendientes, valorizado, próximos a vencer, inventario sin rotación y sugerencias de traslado. Para publicarlo usé un dataset ficticio, de modo que no se expone ningún dato de la organización.

## 7\. Retos técnicos resueltos

* **Archivos de gran tamaño:** lectura por chunks (HTTP Range requests) para procesar archivos que superan los límites de memoria de Apps Script.
* **Límite de 6 minutos de ejecución:** procesamiento por lotes, retomando automáticamente en la siguiente corrida donde quedó pendiente.
* **Volumen histórico:** purga automática de datos con más de 3 meses para no exceder los límites de celdas de Google Sheets, conservando el histórico completo en la fuente original.
* **Escalabilidad de la arquitectura:** al acercarse al límite de celdas, separé los datos crudos de rotación y pendientes en un archivo aparte, dejando el archivo principal solo con las hojas de análisis.
* **Trazabilidad:** log de archivos procesados y hoja de errores dedicada, para evitar reprocesos duplicados y detectar fallos sin detener el sistema.

## 8\. Resultados

* **Reducción de tiempo:** de 12-15 horas semanales a 30-40 minutos para redactar las órdenes de traslado de todas las sedes.
* **160-190 millones de pesos en riesgo de vencimiento identificados** en los primeros 3 meses, un valor que antes no tenía visibilidad centralizada ni oportuna. *(Es riesgo identificado, no pérdida evitada: por eso el sistema exige evidencia de cada traslado.)*
* **Reducción de casi un 50% en el valor de pérdida mensual por vencimiento**, validada desde las primeras semanas, cuando las sugerencias comenzaron a cubrir pendientes reales.
* **Más de 10 sugerencias de traslado por sede, cada semana.**
* **3 meses en producción**, usado para decisiones reales del equipo de inventarios.

## 9\. Uso de IA en el proyecto

Usé IA como herramienta de trabajo para acelerar el desarrollo del código, la documentación y la exploración de los datos. Las **reglas de negocio, los criterios de riesgo, la validación de los resultados y las decisiones de operación son mías**: la IA aceleró la construcción, pero yo definí qué debía resolver el sistema y comprobé que lo hiciera bien.

## 10\. Próximos pasos

Seguir reduciendo la intervención manual en el envío y seguimiento de las órdenes de traslado, para acelerar la respuesta ante inventario en riesgo.

## Herramientas y habilidades

Google Apps Script · Google Sheets · Looker Studio · Automatización de procesos · Análisis de datos · Definición de KPIs · Clasificación de riesgo · Lógica de optimización de recursos

\---

*Este caso describe la arquitectura y el impacto del proyecto sin exponer código fuente, datos de pacientes ni información confidencial de la organización.*

