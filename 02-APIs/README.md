## APIs Estructurados de Apache Spark

### Capítulo 3. APIs Estructurados de Apache Spark

En este módulo, exploraremos las motivaciones principales detrás de agregar estructura a Apache Spark, cómo estas motivaciones llevaron a la creación de APIs de alto nivel (DataFrames y Datasets) y su unificación en Spark 2.x. También veremos el motor Spark SQL que sustenta estas APIs estructuradas de alto nivel.

Cuando se introdujo Spark SQL en las primeras versiones de Spark 1.x, seguido por DataFrames como sucesores de SchemaRDDs en Spark 1.3, vimos por primera vez la estructura en Spark. Spark SQL presentó funciones operativas de alto nivel expresivas, imitando la sintaxis de SQL, y los DataFrames, que allanaron el camino para operaciones más estructuradas y eficientes en las consultas computacionales de Spark.

### Spark: ¿Qué hay debajo de un RDD?

El RDD es la abstracción más básica en Spark, caracterizado por:

- **Dependencias**
- **Particiones** (con alguna información de localización)
- **Función de cómputo**: `Partition => Iterator[T]`

Estas características permiten que Spark maneje la resiliencia, la paralelización del trabajo y el procesamiento de datos. Sin embargo, el modelo original de RDD tiene algunas limitaciones, ya que Spark no puede optimizar la función de cómputo ni sabe qué tipo de datos está manejando.

### Estructurando Spark

Spark 2.x introdujo esquemas clave para estructurar Spark, como expresar cálculos usando patrones comunes de análisis de datos, usar operadores comunes en un DSL y organizar los datos en un formato tabular. Estos esquemas proporcionan beneficios como mejor rendimiento, eficiencia espacial, expresividad, simplicidad y uniformidad.

### Beneficios Clave

Estructurar Spark ofrece beneficios como expresividad y composibilidad. Por ejemplo, comparar un cálculo de promedio de edades usando RDDs y DataFrames muestra claramente la diferencia en simplicidad y claridad del código.

#### Ejemplo con RDD

```python
dataRDD = sc.parallelize([("Brooke", 20), ("Denny", 31), ("Jules", 30), ("TD", 35), ("Brooke", 25)])
agesRDD = (dataRDD
  .map(lambda x: (x[0], (x[1], 1)))
  .reduceByKey(lambda x, y: (x[0] + y[0], x[1] + y[1]))
  .map(lambda x: (x[0], x[1][0]/x[1][1])))
```

#### Ejemplo con DataFrame

```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import avg

spark = (SparkSession.builder.appName("AuthorsAges").getOrCreate())
data_df = spark.createDataFrame([("Brooke", 20), ("Denny", 31), ("Jules", 30), ("TD", 35), ("Brooke", 25)], ["name", "age"])
avg_df = data_df.groupBy("name").agg(avg("age"))
avg_df.show()
```

### API de DataFrame

Los DataFrames en Spark son tablas distribuidas en memoria con columnas nombradas y esquemas, similares a las tablas de SQL o DataFrames de pandas. Los tipos de datos básicos y complejos de Spark permiten definir esquemas detallados y manejar datos complejos y estructurados.

#### Tipos de Datos Básicos y Estructurados

Spark admite tipos de datos básicos como `StringType`, `IntegerType`, y tipos estructurados como `ArrayType`, `StructType`.

#### Crear DataFrames con Esquemas

Definir un esquema programáticamente o usando una cadena DDL es esencial para manejar grandes conjuntos de datos de manera eficiente. Aquí hay un ejemplo en Python:

```python
from pyspark.sql.types import StructType, StructField, StringType, IntegerType, ArrayType

schema = StructType([StructField("author", StringType(), False),
                     StructField("title", StringType(), False),
                     StructField("pages", IntegerType(), False)])

data = [("Jules", "Damji", 4535, ["twitter", "LinkedIn"]),
        ("Brooke", "Wenig", 8908, ["twitter", "LinkedIn"]),
        ("Denny", "Lee", 7659, ["web", "twitter", "FB", "LinkedIn"])]

spark = (SparkSession.builder.appName("Example").getOrCreate())
blogs_df = spark.createDataFrame(data, schema)
blogs_df.show()
```

### Columnas y Expresiones

Las columnas en DataFrames son objetos con métodos públicos y se pueden usar en expresiones lógicas o matemáticas. Aquí hay algunos ejemplos de operaciones con columnas:

```python
from pyspark.sql.functions import col, expr

blogs_df.select(expr("Hits * 2")).show(2)
blogs_df.withColumn("Big Hitters", (expr("Hits > 10000"))).show()
blogs_df.withColumn("AuthorsId", (concat(expr("First"), expr("Last"), expr("Id")))).select(col("AuthorsId")).show(4)
```

### Filtrado y Proyección

Las proyecciones y filtros se realizan con los métodos `select()` y `filter()` o `where()`:

```python
few_fire_df = (fire_df.select("IncidentNumber", "AvailableDtTm", "CallType").where(col("CallType") != "Medical Incident"))
few_fire_df.show(5, truncate=False)
```

### Agregaciones

Las operaciones de agrupación y agregación son comunes en análisis de datos:

```python
from pyspark.sql.functions import countDistinct

(fire_df.select("CallType").where(col("CallType").isNotNull()).agg(countDistinct("CallType").alias("DistinctCallTypes")).show())
```

### Resumen

En este capítulo, exploramos los beneficios y operaciones comunes de los APIs estructurados de Spark, incluyendo DataFrames y Datasets. También discutimos cuándo usar DataFrames, Datasets y RDDs, y examinamos el motor subyacente Spark SQL y su optimizador Catalyst. Estos conceptos sientan las bases para los próximos capítulos, donde profundizaremos en la interoperabilidad entre DataFrames, Datasets y Spark SQL.
