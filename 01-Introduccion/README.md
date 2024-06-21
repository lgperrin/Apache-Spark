# Primeros pasos: Descargar y Utilizar Apache Spark

En este módulo, configuraremos Spark y realizaremos tres sencillos pasos para comenzar a escribir nuestra primera app independiente. Usaremos el modo local, 
donde todo el procesamiento se realiza en una sola máquina usando un _shell_ de Spark. Esto es ideal para aprender, proporcionando una retroalimentación rápida 
para realizar operaciones iterativas con Spark. El modo local no es adecuado para conjuntos de datos grandes; para trabajos reales, es mejor usar modos de despliegue 
como YARN o Kubernetes.

## Paso 1: Descargar Apache Spark

Para comenzar, hay que ir a la página de descargas de Spark, seleccionar “_Pre-built for Apache Hadoop 2.7_” en el menú desplegable y haz clic en “_Download Spark_”. 
Esto descargará un archivo tarball `spark-3.0.0-preview2-bin-hadoop2.7.tgz`, que contiene todos los binarios relacionados con Hadoop necesarios para ejecutar Spark en 
modo local en tu ordenador.

No obstante, si solo deseas aprender Spark en Python, puedes instalar PySpark desde el repositorio PyPI ejecutando `pip install pyspark`. Para instalar dependencias 
adicionales para SQL, ML y MLlib, usa `pip install pyspark[sql,ml,mllib]`.

### Requisitos Previos
Necesitarás instalar Java 8 o superior y configurar la variable de entorno `JAVA_HOME`. Consulta la documentación para obtener instrucciones detalladas sobre cómo 
descargar e instalar Java.

### Estructura de Directorios y Archivos de Spark
Una vez descargado el tarball, descomprímelo y navega al directorio descomprimido. Dentro encontrarás varios archivos y directorios importantes, como:

* bin: scripts para interactuar con Spark
* sbin: scripts administrativos
* examples: ejemplos de código
* data: archivos de texto para pruebas


## Paso 2: Usar el Shell de Scala o PySpark
Spark incluye cuatro intérpretes interactivos: pyspark, spark-shell, spark-sql y sparkR. 
Estos shells permiten análisis de datos ad hoc. Para iniciar PySpark, navega al directorio bin y ejecuta pyspark. 

### Ejemplo Básico en Scala

```scala
val strings = spark.read.text("../README.md")
strings.show(10, false)
strings.count()
```

### Ejemplo Básico en Python

```python
strings = spark.read.text("../README.md")
strings.show(10, truncate=False)
strings.count()
```

## Paso 3: Entender Conceptos Clave de Aplicaciones Spark
Para comprender cómo se ejecuta el código de Spark, es importante familiarizarse con términos clave:

* Aplicación: Programa de usuario construido con las API de Spark.
* SparkSession: Objeto que proporciona un punto de entrada para interactuar con Spark.
* Job: Computación paralela que consiste en múltiples tareas.
* Stage: Subconjunto de tareas dentro de un Job.
* Task: Unidad de trabajo que se envía a un ejecutor de Spark.

### Transformaciones y Acciones
Las operaciones de Spark se clasifican en transformaciones (que transforman un DataFrame en uno nuevo) y acciones (que disparan la evaluación de transformaciones). 
Las transformaciones se evalúan de manera perezosa, permitiendo optimizaciones.

#### Ejemplo de Transformación y Acción en Python

```python
strings = spark.read.text("../README.md")
filtered = strings.filter(strings.value.contains("Spark"))
filtered.count()
```

### La Interfaz de Usuario de Spark
Spark incluye una interfaz gráfica (UI) que permite inspeccionar y monitorear aplicaciones Spark. 
La UI se ejecuta por defecto en el puerto 4040 y muestra detalles sobre Jobs, Stages y Tasks.

## Ejemplo Completo: Contando M&Ms
Supongamos que queremos contar y agregar los colores de M&Ms preferidos en diferentes estados. Aquí un ejemplo en Python:

```Python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("PythonMnMCount").getOrCreate()
mnm_file = "data/mnm_dataset.csv"
mnm_df = spark.read.format("csv").option("header", "true").option("inferSchema", "true").load(mnm_file)
count_mnm_df = mnm_df.select("State", "Color", "Count").groupBy("State", "Color").sum("Count").orderBy("sum(Count)", ascending=False)
count_mnm_df.show(n=60, truncate=False)
```






