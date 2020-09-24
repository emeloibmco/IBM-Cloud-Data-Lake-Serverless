# **IBM Cloud Data Lake Serverless**

Un Data Lake en la nube es un mecanismo elástico, versátil y nativo de la nube que suministra grandes volúmenes de datos a la analítica. La solución presentada a continuación incluye tecnología de código abierto, rentable y de nivel empresarial para lograr una toma de decisiones más inteligente, ágil y basada en datos.

## Contenido
1. [Ingesta de datos](#1-Ingesta-de-datos)
2. [Almacenamiento de datos](#2-Almacenamiento-de-datos)
3. [Acceso a los datos](#3-acceso-a-los-datos)
4. [Análisis de datos](#4-análisis-de-datos)

## Pre-requisitos
* Instale [_Aspera Connect_](https://downloads.asperasoft.com/connect2/?_ga=2.219643609.1934394408.1600868038-579675947.1600868038)
* Cree una instancia de [_SQL Query_](https://cloud.ibm.com/catalog/services/sql-query) - [Guía](https://www.youtube.com/watch?list=PLzpeuWUENMK2R9CqhF0eJDSxfPBi6JeXA&time_continue=5&v=_fMEyqRC__c&feature=emb_title)
* Cree una instancia de [_Watson Studio_](https://cloud.ibm.com/catalog/services/watson-studio) - [Guía](https://www.youtube.com/watch?v=hwsCkVPoeV0)
* Cree una instancia de [_Cloud Object Storage_](https://cloud.ibm.com/objectstorage/create) - [Guía](https://cloud.ibm.com/docs/cloud-object-storage/basics?topic=cloud-object-storage-provision)
* Cree una instancia de Cognos Dashboard Embedded [_Cognos Dashboard Embedded_](https://cloud.ibm.com/catalog/services/ibm-cognos-dashboard-embedded) - [Guía](https://cloud.ibm.com/docs/cognos-dashboard-embedded?topic=cognos-dashboard-embedded-gettingstartedtutorial)

## **1\. Ingesta de datos**

Para la ingesta de datos se utilizará IBM Aspera on Cloud, que es un servicio alojado para enviar y compartir de forma rápida y confiable sus archivos y conjuntos de datos de cualquier tamaño y tipo, a velocidades que son hasta cientos de veces más rápidas que las del FTP y el HTTP.

1. Para instalar Aspera ingrese [aquí](https://downloads.asperasoft.com/connect2/?_ga=2.11352948.1934394408.1600868038-579675947.1600868038).
2. De clic sobre el botón **_"Descargar Aspera Connect"_**.
3. Instale la extensión de Aspera para chrome.
4. Descargue el instalador de Aspera y ejecútelo.

![](https://github.com/javierjimenezm/data-lake/blob/master/DATA%20-%20LAKE/AsperaIBM.gif)

## **2\. Almacenamiento de datos**

Los datos serán almacenados en un IBM Cloud Object Storage, el cual permite almacenar cantidades prácticamente ilimitadas de datos, de forma sencilla y rentable. 

1. Para crear el Bucket donde se almacenarán los datos ingrese a la lista de recursos y de clic sobre su instancia de Object Storage para ver su configuración, luego en la pestaña **Buckets** ó **Depósitos** seleccione **Crear depósito**. Elija un depósito personalizado, regional, estándar y proporcione una ubicación y un nombre único.
2. Repita el paso anterior para crear un nuevo Bucket donde serán almacenados los resultados de la consulta mediante SQL Query.
3. Descargue el archivo [DatosDemoDataLake.CSV](https://github.com/JulianaLeonGonzalez/Data-Lake/blob/master/DatosDemoDataLake.csv) que contiene 80 datos sobre accidentes de tráfico en Los Angeles, tomados de [City of Los Angeles / Traffic Collision Data from 2010](https://data.lacity.org/api/views/d5tf-ez2w/rows.csv?accessType=DOWNLOAD).
4. Ingrese al bucket de datos y arrastre el archivo CSV. Aspera subirá el archivo rápidamente y le informará el estado en todo momento.

![](https://user-images.githubusercontent.com/60897075/94153560-8b80eb80-fe42-11ea-84ca-e54ae223bcf7.gif)

## **3\. Acceso a los datos**

IBM Cloud SQL Query es un servicio totalmente gestionado que le permite ejecutar consultas SQL para analizar, transformar o limpiar datos. Los datos de entrada pueden ser archivos CSV, JSON, ORC, Parquet o AVRO almacenados en un Cloud Object Storage. Cada resultado de la consulta se escribe en un objeto CSV, JSON, ORC, Parquet o AVRO, en una instancia de Cloud Object Storage de su elección. Puede ejecutar consultas y recuperar información sobre su estado mediante la UI de SQL Query, REST API de SQL Query, desde aplicaciones y Notebooks de Python utilizando el paquete ibmcloudsq o mediante IBM Cloud functions. A continuación encontrará  el detalle de dos de los métodos mencionados.

### **A. UI de SQL Query**

1\. En la **Lista de recursos** seleccione su instancia de **IBM SQL Query,** en esta página encontrará información general sobre su recurso.

2\. Pulse el botón **Launch SQL Query UI,** esto abrirá un dashboard desde el cual puede ejecutar sus consultas, ver un historial de trabajos y ver los resultados y detalles de las consultas. Adicionalmente en la parte derecha de la página encontrará la pestaña **Samples,** que contiene diferentes ejemplos de consultas que pueden ayudarlo a trabajar con esta herramienta.

3\. Copie el siguiente fragmento de código, el cual obtiene el id, fecha, edad, sexo, latitud y longitud del archivo **DatosDemoDataLake.csv,** que fue almacenado en el bucket **bucket-data-lake** en la sección **Almacenamiento de datos** de este repositorio.

```sql
SELECT
    ID as id,
    DateOccurred as fecha,
    VictimAge as edad,
    VictimSex as sexo, 
    Latitude as latitude,
    Longitude as longitude
FROM cos://us-south/bucket-data-lake/DatosDemoDataLake.csv
INTO cos://us-south/bucket-data-lake-result
```

4\. Pulse el botón **Run** y en la sección **Result** verifique que los resultados  sean correctos. Finalmente en **Details** encontrará la variable **Result Location** que indica en qué bucket del Object Storage se guardaron los resultados de la consulta.

![](https://user-images.githubusercontent.com/60897075/93942162-7ba7c100-fcf5-11ea-8c81-fda38c44ffcb.gif)

### **B. Watson Studio: Notebooks de Python**

A continuación se describe el paso a paso de otra opción muy práctica para ejecutar consultas en SQL Query, esta vez el proceso se hará desde un Notebook de Python en Watson Studio.

1\. Dentro de su instancia de Watson Studio pulse en **Crear un proyecto** y luego seleccione **Crear un proyecto vacío**. Tendrá que especificar un nombre para el proyecto y seleccionar su Object Storage en la opción de **Almacenamiento**.

2\. De clic en **Añadir a proyecto** y elija la opción **Notebook.** Tendrá que especificar un nombre para el Notebook y puede dejar el **Idioma** y el **Tiempo de ejecución** con los valores predeterminados.

3\. Para interactuar con SQL Query, usaremos **ibmcloudsql** , una biblioteca Python de código abierto que envía consultas SQL a IBM SQL Query, también usaremos **PixieDust** para visualizar los resultados. Instálelos mediante el siguiente código:

```python
!pip install --upgrade ibmcloudsql
!pip install --upgrade pixiedust
```

4\. Importe los paquetes instalados en el paso anterior.

```python
import ibmcloudsql
import getpass
```

5\. Para tener acceso a los recursos de su cuenta de IBM Cloud cree una API Key desde la página de IBM Cloud ingresando en **Gestionar, Acceso (IAM)** y luego en **Claves API** presione **Crear una clave de API de IBM Cloud.** Para finalizar de un nombre a su clave y cópiela para usarla en el siguiente paso.

6\. Para guardar la API  Key de forma segura en el Notebook usaremos el módulo **getpass.** Pegue la clave que obtuvo en el paso anterior.

```python
import getpass
cloud_api_key = getpass.getpass('Enter your IBM Cloud API Key')
```

7\. La primera variable del siguiente fragmento de código permite la conexión con nuestra instancia de SQL Query y puede obtenerla al ingresar a ella, en la sección **Deployment details** como **CRN.** La segunda variable especifica el Bucket donde se almacenan los resultados de la consulta, para conocer su valor puede ingresar a la instancia de Object Storage y en el menú de tres puntos del bucket de resultados, seleccione la opción **URL de SQL.** Recuerde reemplazar los datos de ejemplo dados a continuación, por sus propios valores.

```python
sql_crn = 'crn:v1:bluemix:public:sql-query:us-south:a/3603bccc6f59473faeadbe7b772669a2:d44e6a9a-fff6-4172-8d4c-243489f84317::' 
sql_cos_endpoint = 'cos://us-south/bucket-data-lake-result'
```

8\. En el siguiente recuadro encontrará el código a utilizar para crear la misma petición creada en el paso 3 de la sección **UI de SQL Query** del presente repositorio.

```python
sqlClient = ibmcloudsql.SQLQuery(cloud_api_key, sql_crn, sql_cos_endpoint)

data_source = "cos://us-south/bucket-data-lake/DatosDemoDataLake.csv"

query = """
SELECT
    ID as id,
    DateOccurred as fecha,
    VictimAge as edad,
    VictimSex as sexo, 
    Latitude as latitude,
    Longitude as longitude
FROM cos://us-south/bucket-data-lake/DatosDemoDataLake.csv
INTO cos://us-south/bucket-data-lake-result""".format(data_source)
accidentes_trafico = sqlClient.run_sql(query)
```

9\. El resultado de la petición anterior es almacenado en la variable **accidentes\_trafico,** por lo que puede usar esta variable en la función **.head** y ver las primeras filas de los datos, para comprobar que los pasos anteriores fueron exitosos.

```python
accidentes_trafico.head()
```

![](https://user-images.githubusercontent.com/60897075/93955866-59726b00-fd16-11ea-95f8-10ec14cab873.gif)

## 4\. Análisis de datos

Una de las ventajas destacadas del uso de los Notebooks de python para las consultas SQL es que desde allí también podemos analizar y  visualizar los datos mediante los gráficos más adecuados, por lo que en esta sección la primera opción descrita para el análisis y visualización de datos es el uso de un Notebook de Python en Watson Studio.

### **A. Watson Studio: Notebooks de Python**

1\. Una vez se accede a los datos, mediante los pasos descritos en la sección **Acceso a los datos** del presente repositorio, podemos visualizarlos mediante el código mostrado a continuación.

```python
display(accidentes_trafico)
```

2\. Seleccione **Map** y elija **longitude** y **latitude** como **Keys** y f**echa** y **sexo** como **Values.** Ya con esto podrá visualizar en el mapa los puntos donde ocurrieron los accidentes descritos en los datos y la edad y sexo de la persona que sufrió el accidente.

![](https://user-images.githubusercontent.com/60897075/93956888-bff88880-fd18-11ea-918f-4f1df56d4bb1.gif)

### B. Cognos Dashboard Embedded

IBM Cognos Dashboard Embedded es otra destacada alternativa para el análisis de datos en un Data Lake, ya que ofrece a los desarrolladores la capacidad de integrar una plataforma de visualización directamente en su aplicación y definir el flujo de trabajo y controlar las opciones disponibles para el usuario. Para la visualización de los datos revise los siguientes pasos.

1\. Clone el repositorio donde se encuentra la aplicación de IBM con el Dashboard de Cognos e ingrese a la carpeta de la aplicación.

```shell
git clone https://github.com/IBM-Cloud/nodejs-data-lake-dashboard.git
cd nodejs-data-lake-dashboard
```

2\. En el archivo **manifest.yml** se declaran los servicios vinculados  a su aplicación, siendo dashboard-nodejs-dde el alias de la instancia de IBM SQL Query y dashboard-nodejs-cos el alias de la instancia de IBM Cloud Object Storage. Para crear estos alias utilice los siguientes comandos:

```shell
ibmcloud resource service-alias-create dashboard-nodejs-dde --instance-name <SQL_Query_Instance>
ibmcloud resource service-alias-create dashboard-nodejs-dde --instance-name <Object_Storage_Instance>
```

3\. Instale los paquetes y dependencias y suba la aplicación a su organización de Cloud Foundy.

```shell
npm install
npm run push
```

4\. Para ver la ruta de acceso a su aplicación ejecute el siguiente comando y lleve el valor **route** a su navegador.

```
ibmcloud cf app dashboard-nodejs routes
```

5\. Elija la plantilla que se ajuste a sus necesidades y luego de clic en el botón **Add a source,** esto abrirá una lista de los buckets presentes en su Object Storage, seleccione el bucket de resultados y luego de clic sobre el **jobid.**

6\. Una vez finalice el paso 5 podrá ver los datos resultantes de su consulta del paso 4 de la sección **UI de SQL Query,** para agregar un gráfico pulse el segundo icono del menú lateral y seleccione **Map,** luego arrastre los datos de **longitude** y **latitude** en la configuración del gráfico.

![](https://user-images.githubusercontent.com/60897075/94026099-ddf8d400-fd7e-11ea-9079-6c1093855b81.gif)


## Referencias

* Para tener más información sobre IBM SQL Query ingrese a: [SQL Query Docs](https://cloud.ibm.com/docs/sql-query)
* Para tener más información sobre IBM Object Storage ingrese a: [Object Storage Docs](https://cloud.ibm.com/docs/cloud-object-storage)
* Para tener más información sobre IBM Watson Studio ingrese a: [Notebooks Watson Studio Docs](https://dataplatform.cloud.ibm.com/docs/content/wsj/analyze-data/notebooks-parent.html?context=analytics)
* Para tener más información sobre IBM Cognos Dashboard  a: [Cognos Dashboard Embedded](https://cloud.ibm.com/docs/cognos-dashboard-embedded?topic=cognos-dashboard-embedded-overview_of_cde) ó [Cognos Dashboard](https://dataplatform.cloud.ibm.com/docs/content/wsj/analyze-data/analytics-dashboard.html?context=analytics)
* La aplicación utilizada en la sección Cognos Dashboard Embedded fue tomada de: [nodejs-data-lake-dashboard](https://github.com/IBM-Cloud/nodejs-data-lake-dashboard)
* El código utilizado en la sección Watson Studio: Notebooks de Python fue tomado de:  [cloud-sql-query](https://github.com/ibm-watson-data-lab/cloud-sql-query) 
* Los datos sobre accidentes de tráfico usados en el repositorio fueron tomados de: [Los Angeles Traffic Collision Data from 2010 to Present](https://catalog.data.gov/dataset/traffic-collision-data-from-2010-to-present/resource/643d0e98-5f40-4db3-8427-02641dd05fd9?inner_span=True)

## Autores ✒️
IBM Cloud Tech Sales

