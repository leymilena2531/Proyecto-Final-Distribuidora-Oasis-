# 📊 ANÁLISIS DE NEGOCIO Y GESTIÓN DE INVENTARIO - Área Ingeniería de Datos. 

## 🎯 Objetivo Principal: Automatizar la ingesta de datos nuevos. 

Se realizó la automatización de los registros nuevos cada 24 horas. Este proceso permitirá a Distribuidora Oasis mantener sus datos de inventario y ventas siempre actualizados, optimizando la gestión de su cadena de suministro y mejorando la toma de decisiones estratégicas. Estos datos se ven representados en Power BI, proporcionando visualizaciones y análisis en tiempo real.

☑️ Beneficios:

1. **Eficiencia mejorada:** Elimina la necesidad de procesos manuales repetitivos, reduciendo el tiempo y esfuerzo necesarios para la actualización de datos.

2. **Disponibilidad de datos en tiempo real:** Proporciona acceso a datos actualizados en tiempo real, esencial para la toma de decisiones rápidas y eficaces. Esto es crucial para gestionar el inventario de manera óptima, evitando tanto el exceso como la falta de stock.
   
4. **Escalabilidad:** Facilita la gestión de grandes volúmenes de datos y permite aumentar la capacidad según las necesidades del negocio sin comprometer el rendimiento. Esto asegura que la infraestructura tecnológica pueda crecer al mismo ritmo que la empresa.


## GOOGLE CLOUD PLATFORM (GCP) 

Es una plataforma integral de computación en la nube ofrecida por Google que proporciona una amplia gama de servicios de infraestructura, almacenamiento, bases de datos, análisis de datos, inteligencia artificial y aprendizaje automático para empresas de todos los tamaños y sectores.

## Información del proyecto en GCP: 

**Nombre del Proyecto** `Soluciones Analíticas PF`

**ID de proyecto:** `neural-ripple-426817-v0`

**Conjunto de Datos:** `BD_OASIS` 

**Permisos:** `Roles` modo editor en IAM y Administración, para todas las áreas involucradas en el proyecto. 

**API´s utilizadas:** puede verlas [AQUÍ](https://github.com/leymilena2531/Proyecto-Final-Distribuidora-Oasis-/blob/master/Ingenieria%20de%20Datos/API.md)

## Recursos utilizados dentro de Cloud:

**BigQuery:** utilizado como Datawarehouse. Crear esquema de tablas, insertar datos y consultas SQL. 

**Cloud Storage:** almacenar las credenciales en un Bucket.

**Pub/sub:**

**Cloud Functions:**

**Cloud Scheduler:**

## PIPELINE:

Es una serie de pasos que los datos atraviesan desde su origen hasta su destino final, pasando por procesos de extracción, transformación y carga (ETL). 

El pipeline de Distribuidora Oasis automatiza los datos asegurando que los datos de inventario y ventas sean recopilados, procesados y cargados en el sistema de gestión de datos de manera eficiente y confiable cada 24 horas. Esto incluye:

### 🛢️ EXTRACCIÓN: 

Los archivos CSV almacenados en Google Drive, que salieron de la limpieza de datos por el Área de Ánálisis de Datos(link), se importaron a [Google Cloud Platform](https://cloud.google.com/?_gl=1*6gcnrv*_up*MQ..&gclid=CjwKCAjw-O6zBhASEiwAOHeGxXc4YZx6SNH1EHwvQgGmacSJnslZSK8XEbOaI-IYDAFV-nnJz4emIxoCwYcQAvD_BwE&gclsrc=aw.ds&hl=es_419), en Datawarehouse BigQuery, utilizando código de Python en [Google Colab](https://colab.research.google.com/drive/1j-HrMwga8oIaSLumfFZ1qPX-bo347MU1) 

![Pipeline drawio (3) 1](https://github.com/leymilena2531/Proyecto-Final-Distribuidora-Oasis-/assets/139195222/f7592c5f-a369-41fd-afa6-7c8c0f7800c3)

### 🛠️ TRANSFORMACIÓN: 

**Realizada en BigQuery.**

#### 1️⃣ Crear la estructura del esquema de 2 tablas nuevas: MOVIMIENTO_DE_INVENTARIO e INVENTARIO_REAL. 

**Crear Esquema de Tabla de Movimiento de Inventario**

```sql
-- CREAR ESQUEMA DE TABLA DE MOVIMIENTO DE INVENTARIO
1  CREATE TABLE `neural-ripple-426817-v0.BD_OASIS.MOVIMIENTO_DE_INVENTARIO` (
2      MovimientoID INT64,
3      InventarioID STRING,
4      Detalle_compraID INT64,
5      VentaID INT64,
6      TiendaID INT64,
7      ProductoID INT64,
8      Cantidad INT64
9  );
```

#### Crear Esquema de Tabla de Inventario Real

```sql
-- CREAR ESQUEMA DE TABLA DE INVENTARIO REAL
1  CREATE TABLE `neural-ripple-426817-v0.BD_OASIS.INVENTARIO_REAL` (
2      InventarioID STRING,
3      TiendaID INT64,
4      ProductoID INT64,
5      Stock INT64,
6      Fecha_fin DATE
7  );
```

#### 2️⃣ Ingresar datos a las tablas previamente creadas. 

**Insertar datos en Tabla de Inventario Real**

```sql
-- Calcular el inventario real sumando compras y restando ventas
1  INSERT INTO `neural-ripple-426817-v0.BD_OASIS.INVENTARIO_REAL` (InventarioID, TiendaID, ProductoID, Stock, Fecha_fin)
2  SELECT 
3      CONCAT(CAST(mi.TiendaID AS STRING), '_', COALESCE(t.Ciudad, ''), '_', CAST(mi.ProductoID AS STRING)) AS InventarioID,
4      mi.TiendaID,
5      mi.ProductoID,
6      SUM(CASE WHEN mi.VentaID IS NOT NULL THEN -mi.Cantidad ELSE mi.Cantidad END) AS Stock,
7      '2016-12-31' AS Fecha_fin
8  FROM 
9      `neural-ripple-426817-v0.BD_OASIS.MOVIMIENTO_DE_INVENTARIO` AS mi
10 INNER JOIN 
11     `neural-ripple-426817-v0.BD_OASIS.TIENDA` AS t
12 ON 
13     mi.TiendaID = t.TiendaID
14 GROUP BY 
15     mi.ProductoID, mi.TiendaID, t.Ciudad;
```

**Insertar datos en Tabla Movimiento de Inventario**

```sql
1  INSERT INTO `neural-ripple-426817-v0.BD_OASIS.MOVIMIENTO_DE_INVENTARIO` (InventarioID, Detalle_compraID, VentaID, TiendaID, ProductoID, Cantidad)
2  SELECT 
3      InventarioID,
4      NULL AS Detalle_compraID,
5      NULL AS VentaID,
6      TiendaID,
7      ProductoID,
8      Stock AS Cantidad
9  FROM `BD_OASIS.Inventario_Inicial_NO_Coincidencias`

10 INSERT INTO `neural-ripple-426817-v0.BD_OASIS.MOVIMIENTO_DE_INVENTARIO` (InventarioID, Detalle_compraID, VentaID, TiendaID, ProductoID, Cantidad)
11 SELECT 
12     NULL AS InventarioID,
13     Detalle_compraID,
14     NULL AS VentaID,
15     TiendaID,
16     ProductoID,
17     Cantidad
18 FROM `neural-ripple-426817-v0.BD_OASIS.DETALLE_COMPRA`
19 WHERE Fecha_entrega > '2016-01-01';

20 INSERT INTO `neural-ripple-426817-v0.BD_OASIS.MOVIMIENTO_DE_INVENTARIO` (InventarioID, Detalle_compraID, VentaID, TiendaID, ProductoID, Cantidad)
21 SELECT 
22     NULL AS InventarioID,
23     NULL AS Detalle_compraID,
24     VentaID,
25     TiendaID,
26     ProductoID,
27     -Cantidad AS Cantidad
28 FROM `neural-ripple-426817-v0.BD_OASIS.VENTA`
29 WHERE Fecha_venta > '2016-01-01';
```


### 🔂 AUTOMATIZACIÓN: 

Para iniciar con el proceso de automatización se realizaron pasos previos: 
1. Se importo cada archivo CSV a formato Google Sheets, guardados dentro de Google Drive. Donde la empresa ingresa registros nuevos todos los dias. 
2. Asegurarse que el archivo `.json` con las credenciales del proyecto: `neural-ripple-426817-v0-65c1b31ad608.json`, esten guardadas dentro del Bucket en Cloud Storage.

#### Pasos para realizar la **automatización de ingreso de datos nuevos en la tabla COMPRA**: 

1. 👉 **Google Cloud Pub/Sub** (Publish/Subscribe) es un servicio de mensajería en tiempo real y escalable que facilita la comunicación entre diferentes componentes de aplicaciones distribuidas en Google Cloud Platform (GCP).
Pub/Sub puede manejar grandes volúmenes de mensajes y escalar automáticamente según la demanda.

Lo primero que haremos dentro de este servicio es `CREAR TEMA`.

Un tema es un canal de comunicación al que los editores envían mensajes. Los temas son puntos de acceso a los que los mensajes son enviados.

Elegimos un ID del tema por cada accion a realizar.

En este caso realizamos un tema llamado `Compras_oasis`.

Luego pasamos a Cloud Functions. 

2. 👉 **Google Cloud Functions** es una plataforma  para ejecutar código en respuesta a eventos, facilitando la creación de aplicaciones y servicios altamente escalables y eficientes, sin necesidad de gestionar ni aprovisionar servidores.

Dentro, iremos a ` CREAR FUNCION`,  en aspectos básicos, pusimos entorno de 2° gen. El nombre de la función que ibamos a realizar, en nuestro caso `Activar_compras`, elegimos la región southamerica-east1 (São Paulo) que es la región la misma zona horaria que Argentina. 

En Activador, el tipo de activador elegido es Cloud Pub/Sub y el tema  será `Compras_oasis`, el que creamos anteriormente.

En Entorno de ejecución seleccionamos Python 3.10, y en código fuente seleccionamos `Editor directo`.
Dentro de esto se crearan automaticamente dos archivos, `main.py` y `requirements.txt`.
Punto de partida elegimos el nombre de la función que llamaremos a continuacion `¨load_data_to_bigquery¨`.

En `main.py` colocaremos el script Python que ejecute lo que necesitamos. En nuestro caso, llamamos a las entradas con fecha de hoy, de un archivo de Google SpreadSheets guardado en Google Drive, de la tabla COMPRAS. 


```sql
import os
import gspread
from google.oauth2.service_account import Credentials
from google.cloud import bigquery, storage
import pandas as pd
import datetime
import io
import json

def load_data_to_bigquery(event, context):
    """Triggered from a message on a Cloud Pub/Sub topic.
    Args:
         event (dict): Event payload.
         context (google.cloud.functions.Context): Metadata for the event.
    """
    # Nombre del bucket y del archivo en Google Cloud Storage
    BUCKET_NAME = 'archivos_oasis'
    CREDENTIALS_FILE = 'neural-ripple-426817-v0-65c1b31ad608.json'

    # Crear un cliente de Storage
    storage_client = storage.Client()
    bucket = storage_client.bucket(BUCKET_NAME)
    blob = bucket.blob(CREDENTIALS_FILE)

    # Descargar el archivo de credenciales
    credentials_content = blob.download_as_text()
    credentials_json = json.loads(credentials_content)

    # Autenticación con Google Sheets y BigQuery
    credentials = Credentials.from_service_account_info(credentials_json)
    gc = gspread.authorize(credentials)
    client = bigquery.Client(credentials=credentials, project=credentials.project_id)

    # ID del Google Sheet y el nombre de la hoja
    SHEET_ID = '1XUohKTqW13kRUn-HHp5mFaUljs_vCkBM6zXGAk0yNEQ'
    SHEET_NAME = 'Hoja 1'

    # Abre la hoja de cálculo y carga los datos en un DataFrame de pandas
    spreadsheet = gc.open_by_key(SHEET_ID)
    worksheet = spreadsheet.worksheet(SHEET_NAME)
    rows = worksheet.get_all_values()
    df = pd.DataFrame(rows[1:], columns=rows[0])

    # Convierte la columna de fecha a tipo datetime si es necesario
    df['Fecha_compra'] = pd.to_datetime(df['Fecha_compra'])  # Ajusta 'fecha' al nombre real de tu columna de fecha

    # Filtra solo los datos con fecha igual a la fecha actual
    fecha_actual = datetime.date.today()
    df_filtered = df[df['Fecha_compra'].dt.date == fecha_actual]

    if not df_filtered.empty:
        # Define el conjunto de datos y la tabla en BigQuery
        dataset_id = 'BD_OASIS'
        table_id = 'COMPRA'

        # Carga el DataFrame filtrado en BigQuery
        table_ref = client.dataset(dataset_id, project=client.project).table(table_id)
        job_config = bigquery.LoadJobConfig(
            write_disposition=bigquery.WriteDisposition.WRITE_APPEND,
            source_format=bigquery.SourceFormat.CSV,
            autodetect=True,
        )

        # Convierte el DataFrame filtrado a un archivo CSV temporal
        csv_data = df_filtered.to_csv(index=False)

        # Carga los datos desde el CSV temporal a BigQuery
        load_job = client.load_table_from_file(
            io.StringIO(csv_data), table_ref, job_config=job_config
        )

        # Espera a que el trabajo de carga termine
        load_job.result()

        print(f'Datos cargados en BigQuery para la fecha: {fecha_actual}')
    else:
        print('No se encontraron datos para cargar hoy.')

    # Registro del evento recibido
    print(f'Evento recibido: {event}')

    # Ejemplo de registro del contexto
    print(f'Contexto: {context}')

    # Ejemplo de devolución de mensaje para Pub/Sub
    return 'Procesamiento completado.'
```

En `requirements.txt`  se escribe todas las dependencias que el código necesita para ejecutarse correctamente en Google Cloud Functions. 


```sql
google-cloud-storage
google-cloud-bigquery
google-auth
google-auth-oauthlib
google-auth-httplib2
gspread==4.0.1  # Especificar la versión si lo necesitas
pandas==1.4.0   # Especificar una versión de Pandas compatible
numpy==1.22.3   # Especificar una versión de NumPy compatible
```

Luego de esto, haremos probar función.

Una vez que nos avisa que el codigo está correcto, procederemos a irnos pasar a Google Cloud Scheduler.

3. 👉 **Google Cloud Scheduler** es un servicio de planificación de tareas basado en la nube que permite ejecutar trabajos cron (jobs) de manera automatizada y programada. Es ideal para tareas recurrentes y programadas que necesitan ejecutarse a intervalos específicos o en momentos determinados.

En nuestro caso, publicará el Pub/Sub creado inicial. 

Primero, definimos el programa.
Le pondremos un nombre al programa, en nuestro caso será `Actualizar_compras`, elegimos la misma region que veniamos trabajando, le agregamos una breve descripción de lo que hará el programa, y programaremos la frecuencia.

La frecuencia se especifica con el formato Cron, que funciona de la siguiente forma:

```sql
* * * * *
│ │ │ │ │
│ │ │ │ └─ Día de la semana (0 - 7) (Domingo = 0 o 7)
│ │ │ └─── Mes (1 - 12)
│ │ └───── Día del mes (1 - 31)
│ └─────── Hora (0 - 23)
└───────── Minuto (0 - 59)
```

En nuestro caso, para "59 23 * * *" significará que todos los días las 23:59 se ejecutará la acción, donde todos las filas con fecha del dia entrarán a la tabla COMPRA. 

Una vez finalizada la programación, se ejecutará la carga automatica de datos.

## 📊 CARGA: 

Los registros nuevos que se ingresen por día serán actualizados en cada tabla correspondiente en el Datawarehouse, el cual es conectado con Power Bi, para visualizar en tiempo real los datos de manera visual, calculando métricas de negocio, para su mejor entendimiento y eficacia. 


#### Tecnologías y Herramientas Utilizadas a lo largo del pipeline:

Google Drive, Google Spreadsheets, Google Colab, Google Cloud Platform, BigQuery, Cloud Storage, Pub/Sub, Cloud Functions, Cloud Scheduler. Draw.io. 

