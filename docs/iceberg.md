# 🧊 Apache Iceberg

## O que é o Apache Iceberg?

O **Apache Iceberg** é um formato de tabela open-source de alto desempenho, criado pela **Netflix** em 2017 e doado à Apache Software Foundation. Foi projetado para resolver problemas críticos de confiabilidade e escalabilidade em Data Lakes com dezenas de petabytes.

Hoje é suportado pelos principais engines de dados: Apache Spark, Flink, Trino, Hive, Dremio e Snowflake.

---

## Como Funciona?

O Iceberg usa uma estrutura de metadados em três camadas para rastrear cada arquivo de dados:

```
Iceberg Table
├── metadata/
│   ├── v1.metadata.json          ← metadados da tabela
│   ├── v2.metadata.json
│   ├── snap-001-manifest-list    ← lista de snapshots
│   └── manifest-abc123.avro     ← lista de arquivos de dados
└── data/
    ├── part-00000.parquet
    └── part-00001.parquet
```

### Hierarquia de Metadados

```
Table Metadata
    └── Snapshot (versão imutável)
            └── Manifest List
                    └── Manifest File
                            └── Data Files (Parquet / ORC / Avro)
```

---

## Principais Recursos

| Recurso | Descrição |
|---|---|
| **Transações ACID** | Operações atômicas e consistentes |
| **Snapshots** | Cada operação cria um snapshot imutável |
| **Time Travel** | Consultar dados de snapshots anteriores |
| **Schema Evolution** | ADD, DROP, RENAME, REORDER colunas sem reescrever dados |
| **Hidden Partitioning** | Particionamento automático sem expor ao usuário |
| **Partition Evolution** | Alterar o esquema de partição sem migrar dados |
| **MERGE INTO** | Upsert em SQL puro |
| **Multi-engine** | Spark, Flink, Trino, Hive, Dremio |

---

## Configurando o Spark com Iceberg

```python
from pyspark.sql import SparkSession

spark = (
    SparkSession.builder
    .appName("Apache Iceberg")
    .master("local[*]")
    .config(
        "spark.jars.packages",
        "org.apache.iceberg:iceberg-spark-runtime-3.5_2.12:1.5.2"
    )
    .config("spark.sql.extensions",
            "org.apache.iceberg.spark.extensions.IcebergSparkSessionExtensions")
    .config("spark.sql.catalog.local",
            "org.apache.iceberg.spark.SparkCatalog")
    .config("spark.sql.catalog.local.type", "hadoop")
    .config("spark.sql.catalog.local.warehouse", "./data/iceberg/warehouse")
    .getOrCreate()
)
```

---

## Criando Tabelas e Namespaces

```sql
-- Criar namespace (database)
CREATE NAMESPACE IF NOT EXISTS local.ecommerce;

-- Criar tabela Iceberg
CREATE TABLE local.ecommerce.pedidos (
    id         INT,
    cliente_id INT,
    status     STRING,
    total      DOUBLE,
    data       STRING
) USING iceberg;
```

---

## INSERT — Inserindo Dados

```sql
-- INSERT padrão SQL
INSERT INTO local.ecommerce.pedidos VALUES
    (1, 1, 'pendente',  250.00, '2024-01-10'),
    (2, 2, 'aprovado',  180.00, '2024-01-11');

-- INSERT a partir de outro DataFrame
INSERT INTO local.ecommerce.pedidos
SELECT * FROM pedidos_staging;
```

---

## UPDATE — Atualizando Registros

```sql
-- Atualizar status de pedidos pendentes
UPDATE local.ecommerce.pedidos
SET status = 'aprovado'
WHERE status = 'pendente';
```

```python
# Equivalente via PySpark
spark.sql("""
    UPDATE local.ecommerce.pedidos
    SET status = 'aprovado'
    WHERE status = 'pendente'
""")
```

---

## DELETE — Removendo Registros

```sql
-- Remover pedidos entregues
DELETE FROM local.ecommerce.pedidos
WHERE status = 'entregue';
```

---

## MERGE INTO — Upsert

```sql
MERGE INTO local.ecommerce.clientes AS destino
USING clientes_novos AS origem
ON destino.id = origem.id
WHEN MATCHED THEN
    UPDATE SET destino.cidade = origem.cidade
WHEN NOT MATCHED THEN
    INSERT (id, nome, email, cidade, ativo)
    VALUES (origem.id, origem.nome, origem.email, origem.cidade, origem.ativo);
```

---

## Time Travel via Snapshots

```python
# Ver todos os snapshots
spark.sql("""
    SELECT snapshot_id, committed_at, operation
    FROM local.ecommerce.pedidos.snapshots
""").show()

# Ler um snapshot específico
spark.read \
    .option("snapshot-id", 1234567890) \
    .table("local.ecommerce.pedidos") \
    .show()

# Ler por timestamp
spark.read \
    .option("as-of-timestamp", "2024-01-10T00:00:00") \
    .table("local.ecommerce.pedidos") \
    .show()
```

---

## Schema Evolution

```sql
-- Adicionar coluna
ALTER TABLE local.ecommerce.pedidos
ADD COLUMN desconto DOUBLE;

-- Renomear coluna
ALTER TABLE local.ecommerce.pedidos
RENAME COLUMN desconto TO valor_desconto;

-- Remover coluna
ALTER TABLE local.ecommerce.pedidos
DROP COLUMN valor_desconto;
```

---

## Hidden Partitioning

Uma das grandes vantagens do Iceberg é o **particionamento oculto**: o usuário não precisa filtrar pela coluna de partição explicitamente — o Iceberg resolve automaticamente:

```sql
-- Criar tabela com particionamento por mês
CREATE TABLE local.ecommerce.pedidos_particionados (
    id     INT,
    status STRING,
    total  DOUBLE,
    data   TIMESTAMP
) USING iceberg
PARTITIONED BY (months(data));

-- A query abaixo automaticamente usa a partição correta
SELECT * FROM local.ecommerce.pedidos_particionados
WHERE data BETWEEN '2024-01-01' AND '2024-01-31';
```

---

## Iceberg vs Delta Lake

| Recurso | Delta Lake | Apache Iceberg |
|---|---|---|
| Criado por | Databricks | Netflix |
| Fundação | Linux Foundation | Apache Software Foundation |
| Transações ACID | ✅ | ✅ |
| Time Travel | ✅ | ✅ |
| Schema Evolution | ✅ | ✅ (mais completo) |
| Hidden Partitioning | ❌ | ✅ |
| Partition Evolution | ❌ | ✅ |
| Multi-engine nativo | Parcial | ✅ (Spark, Flink, Trino...) |
| Suporte SQL padrão | ✅ | ✅ |
| Formato de metadados | JSON | JSON + Avro |
