# 🔷 Delta Lake

## O que é o Delta Lake?

O **Delta Lake** é um formato de armazenamento open-source desenvolvido pela **Databricks** que adiciona uma camada de confiabilidade ao Data Lake. Ele transforma arquivos Parquet comuns em tabelas transacionais com suporte a **ACID**, **versionamento** e **time travel**.

Lançado em 2019 e doado à Linux Foundation em 2021, hoje é um dos formatos mais adotados em arquiteturas Data Lakehouse.

---

## Como Funciona?

O Delta Lake armazena os dados em **arquivos Parquet** e mantém um **transaction log** (pasta `_delta_log`) com o histórico de todas as operações realizadas:

```
data/delta/pedidos/
├── _delta_log/
│   ├── 00000000000000000000.json   ← versão 0 (INSERT inicial)
│   ├── 00000000000000000001.json   ← versão 1 (UPDATE)
│   └── 00000000000000000002.json   ← versão 2 (DELETE)
├── part-00000-abc123.snappy.parquet
└── part-00001-def456.snappy.parquet
```

---

## Principais Recursos

| Recurso | Descrição |
|---|---|
| **Transações ACID** | Garante consistência mesmo com falhas ou múltiplos escritores |
| **Time Travel** | Consultar versões anteriores dos dados |
| **Schema Enforcement** | Rejeita dados com schema incompatível |
| **Schema Evolution** | Permite adicionar colunas sem recriar a tabela |
| **MERGE / Upsert** | Combina INSERT e UPDATE em uma única operação |
| **Compaction (OPTIMIZE)** | Consolida pequenos arquivos em arquivos maiores |
| **VACUUM** | Remove arquivos antigos para liberar espaço |

---

## Configurando o Spark com Delta Lake

```python
from pyspark.sql import SparkSession
from delta import configure_spark_with_delta_pip

builder = (
    SparkSession.builder
    .appName("Delta Lake")
    .master("local[*]")
    .config("spark.sql.extensions",
            "io.delta.sql.DeltaSparkSessionExtension")
    .config("spark.sql.catalog.spark_catalog",
            "org.apache.spark.sql.delta.catalog.DeltaCatalog")
)

spark = configure_spark_with_delta_pip(builder).getOrCreate()
```

---

## INSERT — Criando e Populando Tabelas

```python
from pyspark.sql.types import *

schema = StructType([
    StructField("id",     IntegerType(), False),
    StructField("nome",   StringType(),  True),
    StructField("status", StringType(),  True),
    StructField("total",  DoubleType(),  True),
])

dados = [
    (1, "Pedido A", "pendente", 250.00),
    (2, "Pedido B", "aprovado", 180.00),
]

df = spark.createDataFrame(dados, schema)
df.write.format("delta").mode("overwrite").save("./data/delta/pedidos")
```

### INSERT com MERGE (Upsert)

```python
from delta.tables import DeltaTable

novos = spark.createDataFrame([(3, "Pedido C", "pendente", 99.90)], schema)
delta_tab = DeltaTable.forPath(spark, "./data/delta/pedidos")

(
    delta_tab.alias("dest")
    .merge(novos.alias("orig"), "dest.id = orig.id")
    .whenNotMatchedInsertAll()
    .execute()
)
```

---

## UPDATE — Atualizando Registros

```python
from delta.tables import DeltaTable

delta_tab = DeltaTable.forPath(spark, "./data/delta/pedidos")

# Atualizar status de todos os pedidos pendentes
delta_tab.update(
    condition = "status = 'pendente'",
    set       = {"status": "'aprovado'"}
)
```

---

## DELETE — Removendo Registros

```python
delta_tab = DeltaTable.forPath(spark, "./data/delta/pedidos")

# Remover pedidos entregues
delta_tab.delete(condition="status = 'entregue'")
```

---

## Time Travel — Consultando Versões Anteriores

```python
# Ver histórico de operações
delta_tab.history().select("version", "timestamp", "operation").show()

# Ler versão específica
spark.read \
    .format("delta") \
    .option("versionAsOf", 0) \
    .load("./data/delta/pedidos") \
    .show()

# Ler por timestamp
spark.read \
    .format("delta") \
    .option("timestampAsOf", "2024-01-10") \
    .load("./data/delta/pedidos") \
    .show()
```

---

## Schema Evolution

```python
from pyspark.sql.functions import lit

# Adicionar nova coluna 'desconto' sem recriar a tabela
df_novo = spark.read.format("delta").load("./data/delta/pedidos") \
    .withColumn("desconto", lit(0.0))

df_novo.write \
    .format("delta") \
    .mode("overwrite") \
    .option("mergeSchema", "true") \
    .save("./data/delta/pedidos")
```

---

## Estrutura do Transaction Log

Cada operação gera um arquivo JSON no `_delta_log`. Exemplo do conteúdo de uma versão:

```json
{
  "commitInfo": {
    "operation": "WRITE",
    "operationParameters": {"mode": "Overwrite"},
    "timestamp": 1704844800000
  },
  "add": {
    "path": "part-00000.snappy.parquet",
    "size": 2048,
    "stats": "{\"numRecords\": 5}"
  }
}
```

---

## Delta Lake vs Tabela Parquet Tradicional

| Recurso | Parquet | Delta Lake |
|---|---|---|
| Transações ACID | ❌ | ✅ |
| UPDATE / DELETE | ❌ | ✅ |
| Time Travel | ❌ | ✅ |
| Schema Enforcement | ❌ | ✅ |
| Schema Evolution | Manual | ✅ |
| MERGE (Upsert) | ❌ | ✅ |
