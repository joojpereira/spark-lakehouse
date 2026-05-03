# 🚀 Apache Spark com Delta Lake e Apache Iceberg

## Sobre este Projeto

Este projeto foi desenvolvido como **Trabalho de Pesquisa** da disciplina de **Arquitetura de Dados**, com o objetivo de explorar na prática as principais tecnologias do ecossistema **Data Lakehouse** moderno.

O cenário escolhido para os experimentos foi um **E-commerce**, com tabelas de clientes, produtos e pedidos — permitindo demonstrar operações reais de inserção, atualização e exclusão de dados.

---

## 🎯 Objetivos

- Compreender a arquitetura Data Lakehouse e sua diferença em relação ao Data Lake tradicional
- Implementar tabelas com **Delta Lake** no Apache Spark e explorar suas funcionalidades
- Implementar tabelas com **Apache Iceberg** no Apache Spark e explorar suas funcionalidades
- Comparar os dois formatos em termos de recursos, sintaxe e casos de uso
- Documentar o ambiente de forma reproduzível

---

## 🗂️ Estrutura do Projeto

```
spark-lakehouse/
│
├── notebooks/
│   ├── delta_lake.ipynb     # Implementação com Delta Lake
│   └── iceberg.ipynb        # Implementação com Apache Iceberg
│
├── docs/                    # Documentação MkDocs (este site)
├── pyproject.toml           # Dependências gerenciadas com UV
├── mkdocs.yml               # Configuração deste site
└── README.md                # Guia de instalação do ambiente
```

---

## 📦 Cenário: E-commerce

### Modelo Entidade-Relacionamento

```
+------------+       +------------+       +------------+
|  clientes  |       |  pedidos   |       |  produtos  |
+------------+       +------------+       +------------+
| id (PK)    |<------| cliente_id |       | id (PK)    |
| nome       |       | id (PK)    |------>| nome       |
| email      |       | produto_id |       | categoria  |
| cidade     |       | quantidade |       | preco      |
| ativo      |       | status     |       | estoque    |
+------------+       | total      |       +------------+
                     | data       |
                     +------------+
```

### Dados de Exemplo

| Tabela    | Registros | Descrição                        |
|-----------|-----------|----------------------------------|
| clientes  | 5         | Clientes cadastrados no sistema  |
| produtos  | 5         | Produtos do catálogo             |
| pedidos   | 7         | Pedidos realizados               |

---

## 🛠️ Tecnologias Utilizadas

| Tecnologia | Versão | Finalidade |
|---|---|---|
| Python | 3.11 | Linguagem base |
| UV | 0.4+ | Gerenciador de pacotes |
| Apache Spark | 3.5.1 | Motor de processamento |
| Delta Lake | 3.2.0 | Formato de tabela open-source |
| Apache Iceberg | 1.5.2 | Formato de tabela open-source |
| JupyterLab | 4.x | Ambiente de notebooks |
| MkDocs Material | 9.x | Documentação (este site) |

---

## 👥 Integrantes

| Nome | GitHub |
|---|---|
| João Pereira | [@joojpereira](https://github.com/joojpereira) |
| Bruno Sabino | [@SabinexX](https://github.com/SabinexX) |
| Filipe Jeremias | [@oEngenh3iro](https://github.com/oEngenh3iro) |

---

## 📚 Referências

- [Apache Spark Docs](https://spark.apache.org/docs/latest/)
- [Delta Lake Docs](https://docs.delta.io/latest/index.html)
- [Apache Iceberg Docs](https://iceberg.apache.org/docs/latest/)
- [Canal DataWay BR](https://www.youtube.com/@DataWayBR)
- [spark-delta (GitHub)](https://github.com/jlsilva01/spark-delta)
- [spark-iceberg (GitHub)](https://github.com/jlsilva01/spark-iceberg)
