## EDA y Limpieza de Datos en Python para Análisis de Inventario

El *Análisis Exploratorio de Datos (EDA)* es una técnica fundamental que permite obtener una comprensión 
más profunda de los conjuntos de datos con los que trabajan. A través del EDA, se exploran patrones, 
se identifican anomalías y se descubren relaciones clave en los datos. A diferencia de confirmar hipótesis, 
el EDA se centra en hacer preguntas y mantener una mente abierta durante la exploración.
Descripción:
Este proyecto demuestra un flujo de trabajo completo de Análisis Exploratorio de Datos (EDA) y limpieza de 
datos en Python utilizando la librería panda. El objetivo es transformar un conjunto de datos de inventario "sucio"
(https://www.kaggle.com/datasets/bhanupratapbiswas/inventory-analysis-case-study) en un formato adecuado para realizar 
análisis posteriores.
Pasos Clave:

👉 **1.	Carga de Datos:**
o	Se utiliza pandas para leer el archivo csv.
o	Se inspeccionan los primeros registros y la información del dataframe (columnas, tipos de datos).

👉 **2.	EDA Inicial:**
o	Se identifican valores faltantes, duplicados, tipos de datos incorrectos y valores atípicos.
o	Se generan estadísticas descriptivas y visualizaciones para comprender la distribución de los datos.

👉 **3.	Limpieza de Datos:**
o	Se manejan valores faltantes (imputación, eliminación).
o	Se eliminan duplicados.
o	Se corrigen tipos de datos (conversión de cadenas a numéricos, fechas, etc.).
o	Se tratan valores atípicos (eliminación, transformación).

👉 **4.	Análisis Post-Limpieza :**
o	Se exploran los datos limpios para descubrir patrones y tendencias.
o	Se pueden realizar análisis más avanzados (correlaciones, agrupaciones, etc.) si es relevante.
