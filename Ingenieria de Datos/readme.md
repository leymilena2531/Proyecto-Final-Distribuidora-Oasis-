# ANÁLISIS DE NEGOCIO Y GESTIÓN DE INVENTARIO

## ÁREA DE INGENIERÍA DE DATOS

### 🎯 Objetivo: automatizar la ingesta de datos nuevos. 

Beneficios: 

**Eficiencia Mejorada:** Elimina la necesidad de procesos manuales repetitivos, reduciendo el tiempo y esfuerzo necesarios para la actualización de datos.

**Disponibilidad de Datos en Tiempo Real:** Proporciona acceso a datos actualizados en tiempo real, lo que es esencial para la toma de decisiones rápida y eficaz.

**Escalabilidad:** 





## PIPELINE DE DATOS

PROCESO ETL: EXTRAER - TRANSFORMAR - CARGAR

EXTRACCIÓN: Los archivos CSV fueron descargados desde [Kaggle](https://www.kaggle.com/datasets/bhanupratapbiswas/inventory-analysis-case-study). 
- 2017PurchasePricesDec.csv
- BegInvFINAL12312016.csv
- EndInvFINAL12312016.csv
- InvoicePurchases12312016.csv
- PurchasesFINAL12312016.csv
- SalesFINAL12312016.csv

TRANSFORMACIÓN: Los datos fueron transformados y limpiados en Python, por el área de Análisis de Datos (ajuntar su readme). Donde quedaron 9 archivos csv: 
- COMPRA.csv
- DETALLE_COMPRA.csv
- INVENTARIO_FINAL.csv
- INVENTARIO_INICIAL.csv
- PRODUCTO_PROVEEDOR.csv
- PRODUCTO.csv
- PROVEEDOR.csv
- TIENDA.csv
- VENTA.csv

ALMACENAMIENTO INTERMEDIO: Los CSV limpios se guardaron en Google Drive.

CARGA EN BIGQUERY: Los archivos CSV almacenados en Google Drive se importaron a [Google Cloud Platform](https://cloud.google.com/?_gl=1*6gcnrv*_up*MQ..&gclid=CjwKCAjw-O6zBhASEiwAOHeGxXc4YZx6SNH1EHwvQgGmacSJnslZSK8XEbOaI-IYDAFV-nnJz4emIxoCwYcQAvD_BwE&gclsrc=aw.ds&hl=es_419), en Datawarehouse BigQuery, utilizando código de Python en [Google Colaboratory](https://colab.research.google.com/drive/1j-HrMwga8oIaSLumfFZ1qPX-bo347MU1) 

(ajuntar pipepline hecho en draw.io) 


## Estructura en GOOGLE CLOUD PLATFORM

Para la carga de datos en BigQuery, se añadió un **proyecto** llamado: Soluciones Analíticas PF, **ID de proyecto:** neural-ripple-426817-v0. Se creó un **conjunto de datos:** BD_OASIS donde fueron añadidas las 9 tablas mediante código de Python desde Google Colab mencionado anteriormente.

Desde *BigQuery* se creó la estructura del esquema de 2 tablas nuevas: MOVIMIENTO_DE_INVENTARIO e INVENTARIO_REAL. 

## Crear Esquema de Tabla de Movimiento de Inventario

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
#CREAR ESQUEMA DE TABLA DE MOVIMIENTO DE INVENTARIO

CREATE TABLE `neural-ripple-426817-v0.BD_OASIS.MOVIMIENTO_DE_INVENTARIO` (
    MovimientoID INT64,
    InventarioID STRING,
    Detalle_compraID INT64,
    VentaID INT64,
    TiendaID INT64,
    ProductoID INT64,
    Cantidad INT64
);
```


```
# CREAR ESQUEMA DE TABLA DE INVENTARIO REAL

CREATE TABLE `neural-ripple-426817-v0.BD_OASIS.INVENTARIO_REAL` (
    InventarioID STRING,
    TiendaID INT64,
    ProductoID INT64,
    Stock INT64,
    Fecha_fin DATE
);
```

APIS UTILIZADAS: 
