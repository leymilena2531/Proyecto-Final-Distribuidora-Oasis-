# Proyecto de Análisis de Datos 📊 

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
Entrada: VENTAS.csv<br>
Salida: Dataframe "ventas"<br>

![image](https://github.com/leymilena2531/Proyecto-Final-Distribuidora-Oasis-/assets/30348827/09029698-6cce-4c54-8a6c-5b53a2991ee2)
<br>
Entrada: ventas.info() Información general del conjunto de datos.<br>
Salida: Resumen de información sobre el conjunto de datos, incluyendo tipos de datos, valores no nulos y uso de memoria.<br>
<br>
![image](https://github.com/leymilena2531/Proyecto-Final-Distribuidora-Oasis-/assets/30348827/07cb96ed-20c8-4746-9714-9b3ad7a8c39e)
<br>

Entrada: ventas.describe() Estadísticas descriptivas del conjunto de datos<br>
Salida: Resumen de estadísticas descriptivas, como recuento, media, desviación estándar, mínimo, percentiles y máximo.<br>

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

## Visualización Gráfica de los Resultados <br>
El grafico de la cantidad de ventas muestra que la mayoría de las ventas de la distribuidora de bebidas se concentran entre 25,000 y 100,000 unidades, con una mediana que indica el valor central de esta distribución.
  
 ![image](https://github.com/leymilena2531/Proyecto-Final-Distribuidora-Oasis-/assets/30348827/b10368ed-2bbb-49b0-be97-2d7ee56830d1)

El diagrama de dispersión de las ventas diarias con límites de outliers proporciona una visión clara de cómo varían las ventas a lo largo del tiempo y ayuda a identificar días con ventas inusualmente altas o bajas.<br>
 ![image](https://github.com/leymilena2531/Proyecto-Final-Distribuidora-Oasis-/assets/30348827/16edaf86-096f-4053-a1ee-e257045017d8)



## Previsión de la demanda: <br>
Podemos definir la cantidad de ventas como demanda. En el gráfico se observa un factor de estacionalidad que afecta de forma semanal y el mensual.
Podemos ver que el primer pico de demanda se produce el 2 de enero, que es jueves/viernes, y que cada jueves/viernes se alcanza el pico y baja el domingo. El pico de demanda se produce el 29 de enero (que de nuevo es jueves/viernes) con 180.000 ventas, pero al acercarse febrero baja instantáneamente y sólo alcanza un máximo de 16.700, lo que supone sólo un 9%. Esto indica que hay estacionalidad mensual.<br>
Por lo que nos centramos en realizar el analisis predictivo en fechas del 01/01/2016 al 31/01/2016

### Filtrado de Datos
En primer lugar, se filtran los datos de ventas para incluir solo aquellas transacciones con una cantidad vendida mayor a cero. Esto elimina registros no relevantes o errores en los datos.
Luego, se seleccionan las ventas correspondientes al mes de enero de 2016.
### Creación del DataFrame
Se agrupan las ventas por fecha ("Fecha_venta") y se calcula la suma total de la cantidad vendida para cada fecha. Esto crea un nuevo DataFrame llamado cantidad_enero.
### Configuración del Proceso Determinístico
Se configura un proceso determinístico para modelar las tendencias y estacionalidades en los datos de ventas. Utilizamos la clase CalendarFourier para crear componentes estacionales (en este caso, frecuencia mensual).
### Normalización de Características
Se utiliza StandardScaler para normalizar las características del proceso determinístico. Esto es importante para que las características tengan una escala comparable y no afecten negativamente el rendimiento del modelo.
### Ajuste del Modelo de Regresión Lineal
Se crea un modelo de Regresión Lineal y se ajusta a las características normalizadas (X_scaled) y la cantidad vendida (cantidad_enero['Cantidad']). El objetivo es predecir la cantidad vendida.
### Predicciones y Gráfico Interactivo
Se realizan predicciones en el conjunto de entrenamiento utilizando el modelo de regresión lineal.
Las predicciones se agregan al DataFrame cantidad_enero como la columna "prediccion".
Se crea un gráfico interactivo utilizando Plot
![image](https://github.com/leymilena2531/Proyecto-Final-Distribuidora-Oasis-/assets/30348827/17a9b6c7-11c8-4a8a-a13c-7b813fdd72f3)
### Cálculo de Métricas de Precisión
<b>Resultados: </b><br>
RMSE: 2657.9381532484854<br>
MAE: 1956.8224091599516<br>
R²: 0.9928043826563062<br>
R² ajustado: 0.9652211828388134<br>

## Función para Predecir Ventas Futuras
Se Realiza predicciones de ventas futuras para un período específico.
<li><b>Parámetros:</b>
fecha_inicio: Fecha a partir de la cual se realizarán las predicciones (formato 'YYYY-MM-DD').
meses_futuros: Número de meses para predecir hacia el futuro.
<li><b>Retorna:</b>
predicciones_futuras: Serie temporal con las predicciones de ventas.

 ###  Predicciones para los Próximos Dos Meses
Se define predecir ventas para los próximos dos meses a partir de enero
predicciones = predecir_ventas('2016-01-01', 2)

### Obtener la Predicción para una Fecha Específica
Definir la entrada de la fecha '2016-01-20' para obtener la predicción de la cantidad de ventas
fecha_especifica = pd.to_datetime('2016-01-20')
<b>Resultado: </b><br>
<b>Predicción venta:</b> 2016-01-20: 53862
