# ⚡ Apache Spark e PySpark

## O que é o Apache Spark?

O **Apache Spark** é um motor de processamento de dados distribuído e de código aberto, projetado para ser rápido e flexível. Criado na Universidade de Berkeley em 2009 e depois doado à Apache Software Foundation, ele se tornou o padrão da indústria para processamento de grandes volumes de dados.

Diferente do Hadoop MapReduce (seu predecessor), o Spark processa dados **em memória (in-memory)**, o que pode ser até **100x mais rápido** para cargas de trabalho iterativas, como machine learning.

---

## Arquitetura

```
+----------------------------------------------------+
|                  Driver Program                    |
|           (SparkContext / SparkSession)             |
+----------------------------------------------------+
                        |
          +-------------+-------------+
          |             |             |
    +----------+  +----------+  +----------+
    | Executor |  | Executor |  | Executor |
    | (Worker) |  | (Worker) |  | (Worker) |
    +----------+  +----------+  +----------+
          |             |             |
    +----------+  +----------+  +----------+
    |  Cache   |  |  Cache   |  |  Cache   |
    +----------+  +----------+  +----------+
```

- **Driver**: coordena a execução, cria o SparkContext
- **Executors**: workers que executam as tarefas em paralelo
- **Cluster Manager**: gerencia os recursos (YARN, Kubernetes, Standalone)

---

## Componentes Principais

| Componente | Descrição |
|---|---|
| **Spark Core** | Motor base, agendamento de tarefas, I/O |
| **Spark SQL** | Consultas SQL e DataFrames |
| **Spark Streaming** | Processamento em tempo real |
| **MLlib** | Biblioteca de machine learning |
| **GraphX** | Processamento de grafos |

---

## O que é PySpark?

**PySpark** é a API Python para o Apache Spark. Ela permite usar todas as funcionalidades do Spark com a sintaxe e o ecossistema Python — incluindo pandas, NumPy e scikit-learn.

---

## Iniciando uma SparkSession

A `SparkSession` é o ponto de entrada para qualquer aplicação Spark moderno:

```python
from pyspark.sql import SparkSession

spark = (
    SparkSession.builder
    .appName("MeuApp")
    .master("local[*]")   # local[*] usa todos os cores disponíveis
    .getOrCreate()
)

print(spark.version)
```

---

## DataFrame API

O principal abstração do Spark SQL é o **DataFrame** — uma coleção distribuída de dados organizada em colunas nomeadas, similar a uma tabela de banco de dados:

```python
from pyspark.sql.types import StructType, StructField, StringType, IntegerType

schema = StructType([
    StructField("nome",  StringType(),  True),
    StructField("idade", IntegerType(), True),
])

dados = [("Ana", 30), ("Bruno", 25), ("Carla", 35)]
df = spark.createDataFrame(dados, schema)

df.show()
# +-----+-----+
# | nome|idade|
# +-----+-----+
# |  Ana|   30|
# |Bruno|   25|
# |Carla|   35|
# +-----+-----+
```

---

## Operações Básicas

```python
from pyspark.sql.functions import col, avg

# Filtrar
df.filter(col("idade") > 28).show()

# Selecionar colunas
df.select("nome", "idade").show()

# Agregar
df.groupBy("categoria").agg(avg("preco").alias("preco_medio")).show()

# Ordenar
df.orderBy("idade", ascending=False).show()
```

---

## Leitura e Escrita de Dados

```python
# Ler CSV
df = spark.read.csv("caminho/arquivo.csv", header=True, inferSchema=True)

# Ler Parquet
df = spark.read.parquet("caminho/dados/")

# Escrever em Delta Lake
df.write.format("delta").mode("overwrite").save("caminho/delta/")

# Escrever em Iceberg
df.write.format("iceberg").mode("append").save("local.catalogo.tabela")
```

---

## Lazy Evaluation

O Spark usa **avaliação preguiçosa (lazy evaluation)**: transformações como `filter()`, `select()` e `groupBy()` não executam imediatamente. Elas constroem um **plano de execução** que só é disparado quando uma **ação** é chamada:

| Tipo | Exemplos |
|---|---|
| **Transformações** (lazy) | `filter`, `select`, `groupBy`, `join`, `withColumn` |
| **Ações** (executam) | `show`, `count`, `collect`, `write`, `save` |

---

## Spark no contexto Data Lakehouse

Neste projeto, o Spark é usado como motor de processamento integrado com dois formatos de tabela open-source:

- **Delta Lake** — via `delta-spark`
- **Apache Iceberg** — via `iceberg-spark-runtime`

Ambos adicionam ao Spark suporte a transações ACID, versionamento e operações DML (INSERT, UPDATE, DELETE) diretamente sobre arquivos no storage.
