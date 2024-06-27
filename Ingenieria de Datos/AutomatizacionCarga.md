Antes de iniciar cualquier proceso de automatización se deberá contar con el archivo .json con las credenciales del proyecto del que vamos a trabajar. En nuestro caso será `neural-ripple-426817-v0-65c1b31ad608`

En primer lugar en Pub/Sub, se creó un tema por cada tarea que vamos a automatizar `CREAR TEMA`

**Google Cloud Pub/Sub (Publish/Subscribe)** es un servicio de mensajería en tiempo real y escalable que facilita la comunicación entre diferentes componentes de aplicaciones distribuidas en Google Cloud Platform (GCP).

Pub/Sub puede manejar grandes volúmenes de mensajes y escalar automáticamente según la demanda.

Lo primero que haremos dentro de este coso es `CREAR TEMA`.

Un tema es un canal de comunicación al que los editores envían mensajes. Los temas son puntos de acceso a los que los mensajes son enviados.

Elegimos un ID del tema por cada accion a realizar.

En este caso realizamos un tema llamado `Compras_oasis`.

Luego pasamos a Cloud Functions. 

**Google Cloud Functions** es una plataforma poderosa y flexible para ejecutar código en respuesta a eventos, facilitando la creación de aplicaciones y servicios altamente escalables y eficientes, sin necesidad de gestionar ni aprovisionar servidores.

Dentro, iremos a `+ CREAR FUNCION`,  en aspectos básicos, pusimos entorno de 2° gen. El nombre de la función que ibamos a realizar, en nuestro caso `Activar_compras`, elegimos la región southamerica-east1 (São Paulo) que es la región la misma zona horaria que argentina. 

En Activador, elegiremos que el tipo de Activador sea Cloud Pub/Sub y el tema elegido será `Compras_oasis`, el que creamos anteriormente.
 y pondremos `SIGUIENTE`.

En Entorno de ejecución seleccionamos Python 3.10, y en codigo fuente seleccionamos `Editor directo`.
Dentro de esto se crearan automaticamente dos archivos, `main.py` y `requirements.txt`.
Punto de partida elegiremos el nombre de la función que llamaremos a continuacion `¨load_data_to_bigquery¨`.

En `main.py` colocaremos el código Python de que ejecute lo que necesitamos. En nuestro caso, estaremos llamando a las entradas con fecha de hoy, de un archivo de Google SpreadSheets guardado en Google Drive, de la tabla COMPRAS. 


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

En `requirements.txt`  se escribiran todas las dependencias que el código necesita para ejecutarse correctamente en Google Cloud Functions. 


```sql
google-cloud-storage
google-cloud-bigquery
google-auth
google-auth-oauthlib
google-auth-httplib2
gspread==4.0.1  # Asegúrate de especificar la versión si lo necesitas
pandas==1.4.0   # Especifica una versión de Pandas compatible
numpy==1.22.3   # Especifica una versión de NumPy compatible
```

Luego de esto, haremos probar función.



Una vez que nos avisa que el codigo está correcto, procederemos a irnos pasar a Google Cloud Scheduler.


**Google Cloud Scheduler** es un servicio de planificación de tareas basado en la nube que permite ejecutar trabajos cron (jobs) de manera automatizada y programada. Es ideal para tareas recurrentes y programadas que necesitan ejecutarse a intervalos específicos o en momentos determinados.

En nuestro caso, publicará el Pub/Sub creado inicial. 


Primero, definimos el programa.
Le pondremos un nombre al programa, en nuestro caso será `Actualizar_compras`, elegimos la misma region que veniamos trabajando, le agregamos una breve descripción de lo que hará el programa, y programaremos la frecuencia.

La frecuencia se especifica con el formato Cron, que funciona de la siguiente  forma

'* * * * *'
│ │ │ │ │
│ │ │ │ └─ Día de la semana (0 - 7) (Domingo = 0 o 7)
│ │ │ └─── Mes (1 - 12)
│ │ └───── Día del mes (1 - 31)
│ └─────── Hora (0 - 23)
└───────── Minuto (0 - 59)


En nuestro caso, para "59 23 * * *" significará que todos los días las 23:59 se ejecutará la acción, donde todos las filas con fecha del dia entrarán a la tabla COMPRA. 
