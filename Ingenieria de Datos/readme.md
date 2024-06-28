# 🌐 ANÁLISIS DE NEGOCIO Y GESTIÓN DE INVENTARIO - Área Ingeniería de Datos. 

## 🎯 Objetivo Principal: Automatizar la ingesta de datos nuevos. 

Se realizó la automatización de los registros nuevos cada 24 horas. Este proceso permitirá a Distribuidora Oasis mantener sus datos de inventario y ventas siempre actualizados, optimizando la gestión de su cadena de suministro y mejorando la toma de decisiones estratégicas. Estos datos se ven representados en Power BI, proporcionando visualizaciones y análisis en tiempo real.

☑️ Beneficios:

1. **Eficiencia mejorada:** Elimina la necesidad de procesos manuales repetitivos, reduciendo el tiempo y esfuerzo necesarios para la actualización de datos.

2. **Disponibilidad de datos en tiempo real:** Proporciona acceso a datos actualizados en tiempo real, esencial para la toma de decisiones rápidas y eficaces. Esto es crucial para gestionar el inventario de manera óptima, evitando tanto el exceso como la falta de stock.
   
4. **Escalabilidad:** Facilita la gestión de grandes volúmenes de datos y permite aumentar la capacidad según las necesidades del negocio sin comprometer el rendimiento. Esto asegura que la infraestructura tecnológica pueda crecer al mismo ritmo que la empresa.

5. **Mejora en la precisión de los datos:** La automatización reduce los errores humanos asociados con la entrada manual de datos, garantizando que la información utilizada para la toma de decisiones sea precisa y fiable.


## GOOGLE CLOUD PLATFORM (GCP) 

Es una plataforma integral de computación en la nube ofrecida por Google que proporciona una amplia gama de servicios de infraestructura, almacenamiento, bases de datos, análisis de datos, inteligencia artificial y aprendizaje automático para empresas de todos los tamaños y sectores.

Información del proyecto en GCP: 

**Nombre del Proyecto** `Soluciones Analíticas PF`

**ID de proyecto:** `neural-ripple-426817-v0`

**Conjunto de Datos:** `BD_OASIS` 

**Permisos:** `Roles` modo editor en IAM y Administración, para todas las áreas involucradas en el proyecto. 

**API´s utilizadas:** puede verlas [AQUÍ](https://github.com/leymilena2531/Proyecto-Final-Distribuidora-Oasis-/blob/master/Ingenieria%20de%20Datos/API.md)

### Recursos utilizados dentro de Cloud:

BigQuery: utilizado como Datawarehouse. Crear esquema de tablas, insertar datos y consultas SQL. 
Cloud Storage: almacenar las credenciales en un Bucket.
Pub/sub:
Cloud Functions: 
Cloud Scheduler: 

# PIPELINE:

Ees una serie de pasos que los datos atraviesan desde su origen hasta su destino final, pasando por procesos de extracción, transformación y carga (ETL). En el contexto de Distribuidora Oasis, el pipeline de datos automatizado asegura que los datos de inventario y ventas sean recopilados, procesados y cargados en el sistema de gestión de datos de manera eficiente y confiable cada 24 horas. Esto incluye:

### EXTRACCIÓN: 

Los archivos CSV almacenados en Google Drive, que salieron de la limpieza de datos por el Área de Ánálisis de Datos(link), se importaron a [Google Cloud Platform](https://cloud.google.com/?_gl=1*6gcnrv*_up*MQ..&gclid=CjwKCAjw-O6zBhASEiwAOHeGxXc4YZx6SNH1EHwvQgGmacSJnslZSK8XEbOaI-IYDAFV-nnJz4emIxoCwYcQAvD_BwE&gclsrc=aw.ds&hl=es_419), en Datawarehouse BigQuery, utilizando código de Python en [Google Colab](https://colab.research.google.com/drive/1j-HrMwga8oIaSLumfFZ1qPX-bo347MU1) 

![Pipeline drawio (3) 1](https://github.com/leymilena2531/Proyecto-Final-Distribuidora-Oasis-/assets/139195222/f7592c5f-a369-41fd-afa6-7c8c0f7800c3)

### TRANSFORMACIÓN:

Desde *BigQuery* se creó la estructura del esquema de 2 tablas nuevas: MOVIMIENTO_DE_INVENTARIO e INVENTARIO_REAL. 

#### Crear Esquema de Tabla de Movimiento de Inventario

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









****Tecnologías y Herramientas Utilizadas

Google Sheets, Google Colab, Google Cloud Platform, BigQuery, 

