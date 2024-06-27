# Proyecto de Análisis de Datos

Este proyecto utiliza un Jupyter Notebook para realizar un análisis de datos detallado. A continuación se describen todas las secciones del notebook y los resultados obtenidos.

## Contenido

1. Introducción
2. Importación de Librerías
3. Carga de Datos
4. Análisis Exploratorio de Datos
5. Modelado Predictivo
6. Evaluación del Modelo
7. Resultados

## Introducción

A partir de los datos de las ventas del 01-01-2016 al 29-02-2016 de una empresa manufacturera dedicada a la distribución de bebidas, se realiza un Modelo Predictivo de Demanda lo cual es fundamental para optimizar la gestión de inventarios.
Esto permitirá mejorar los procesos de compras, previsiones y la toma de decisiones en general.
Los datos ya han sido recolectados, limpiados y transformados Se trata del estudio de una serie temporal: Veremos si se trata de una serie estacionaria o no. En caso de no serlo, haremos las transformaciones necesarias, elegiremos el modelo más apropiado, lo probaremos y ajustaremos.

## Importación de Librerías

Se importan las librerías necesarias para el análisis y modelado de datos, incluyendo `pandas`, `numpy`, `matplotlib`, y `scikit-learn`.

## Concepto del Modelo Predictivo de Demanda
El objetivo del modelo es predecir la demanda futura de productos teniendo en cuenta los datos históricos de lasventas.
Esto permitirá una gestión más eficiente del inventario y una planificación adecuada de reabastecimiento.

## Previsualización de datos
### Visualizamos las primeras filas del conjunto de datos para entender su estructura.
Entrada: VENTAS.csv
Salida: Dataframe "ventas"
![image](https://github.com/leymilena2531/Proyecto-Final-Distribuidora-Oasis-/assets/30348827/09029698-6cce-4c54-8a6c-5b53a2991ee2)
Entrada: ventas.info() Información general del conjunto de datos.
Salida: Resumen de información sobre el conjunto de datos, incluyendo tipos de datos, valores no nulos y uso de memoria
![image](https://github.com/leymilena2531/Proyecto-Final-Distribuidora-Oasis-/assets/30348827/07cb96ed-20c8-4746-9714-9b3ad7a8c39e)
Entrada: ventas.describe() Estadísticas descriptivas del conjunto de datos
Salida: Resumen de estadísticas descriptivas, como recuento, media, desviación estándar, mínimo, percentiles y máximo
![image](https://github.com/leymilena2531/Proyecto-Final-Distribuidora-Oasis-/assets/30348827/8f0768ab-daa4-4533-8113-b6c98878acc2)

## Análisis exploratorio de datos
### Previsión de la demanda
Agrupación de los datos de la tabla venta por la cantidad de producots y Suma por Fecha
Realizar el calculo de la media de las ventas diarias, semanales y mensuales.<br>
<br>
<li><b>Resultados </b><br>
Media Diaria de Ventas: La distribuidora vende, en promedio, 40,853 unidades de su producto por día.<br>
Media Semanal de Ventas: En una semana típica, la distribuidora vende aproximadamente 245,117 unidades. <br>
Media Mensual de Ventas: Mensualmente, la distribuidora vende alrededor de 1,225,584 unidades. <br>
<br>
<li><b>Calculo de Outliers detectados: </b><br>
 Fecha_venta    Cantidad<br>
 2016-01-29     180426 <br>
  
 ![image](https://github.com/leymilena2531/Proyecto-Final-Distribuidora-Oasis-/assets/30348827/b10368ed-2bbb-49b0-be97-2d7ee56830d1)

 ![image](https://github.com/leymilena2531/Proyecto-Final-Distribuidora-Oasis-/assets/30348827/16edaf86-096f-4053-a1ee-e257045017d8)

<br>
##Previsión de la demanda:<br>
Podemos definir la cantidad de ventas como demanda. En el gráfico se observa un factor de estacionalidad que afecta de forma semanal y el mensual.
Podemos ver que el primer pico de demanda se produce el 2 de enero, que es jueves/viernes, y que cada jueves/viernes se alcanza el pico y baja el domingo. El pico de demanda se produce el 29 de enero (que de nuevo es jueves/viernes) con 180.000 ventas, pero al acercarse febrero baja instantáneamente y sólo alcanza un máximo de 16.700, lo que supone sólo un 9%. Esto indica que hay estacionalidad mensual.<br>
Por lo que nos centramos en realizar el analisis predictivo en fechas del 01/01/2016 al 31/01/2016







