# 🔥 Apache Spark com Delta Lake e Apache Iceberg

> Trabalho de Pesquisa — Arquitetura de Dados  
> Tema: Apache Spark com Delta Lake e Apache Iceberg  
> Gerenciador de pacotes: **UV**  
> Sistema Operacional: **Linux Ubuntu**

---

## 📋 Índice

- [Sobre o Projeto](#sobre-o-projeto)
- [Tecnologias Utilizadas](#tecnologias-utilizadas)
- [Pré-requisitos](#pré-requisitos)
- [Instalação do Ambiente](#instalação-do-ambiente)
- [Estrutura do Projeto](#estrutura-do-projeto)
- [Como Executar os Notebooks](#como-executar-os-notebooks)
- [Documentação MkDocs](#documentação-mkdocs)
- [Integrantes](#integrantes)

---

## 📌 Sobre o Projeto

Este projeto demonstra na prática o uso do **Apache Spark** integrado com dois formatos de tabela open-source para Data Lakehouse:

- **Delta Lake**: formato desenvolvido pela Databricks, com suporte a transações ACID, versionamento e time travel.
- **Apache Iceberg**: formato open-source criado pela Netflix, com evolução de schema, particionamento oculto e suporte a múltiplos engines.

O cenário de dados utilizado é um **e-commerce**, com tabelas de clientes, pedidos e produtos. Os notebooks demonstram operações de `INSERT`, `UPDATE` e `DELETE` em ambos os formatos dentro do PySpark.

---

## 🛠 Tecnologias Utilizadas

| Tecnologia | Versão |
|---|---|
| Python | 3.11+ |
| UV (gerenciador de pacotes) | 0.4+ |
| Apache Spark (PySpark) | 3.5.x |
| Delta Lake | 3.2.x |
| Apache Iceberg | 1.5.x |
| JupyterLab | 4.x |
| Java (JDK) | 11 ou 17 |
| MkDocs | 1.5.x |
| MkDocs Material Theme | 9.x |

---

## ✅ Pré-requisitos

### 1. Java (JDK 11 ou 17)

O Apache Spark requer Java instalado na máquina.

```bash
# Verificar se Java já está instalado
java -version

# Instalar OpenJDK 17 (caso não tenha)
sudo apt update
sudo apt install openjdk-17-jdk -y

# Verificar instalação
java -version
```

Configurar a variável de ambiente `JAVA_HOME`:

```bash
# Adicionar ao final do arquivo ~/.bashrc
echo 'export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64' >> ~/.bashrc
echo 'export PATH=$JAVA_HOME/bin:$PATH' >> ~/.bashrc

# Recarregar o shell
source ~/.bashrc

# Verificar
echo $JAVA_HOME
```

---

### 2. UV (Gerenciador de Pacotes Python)

```bash
# Instalar UV
curl -LsSf https://astral.sh/uv/install.sh | sh

# Recarregar o shell
source $HOME/.cargo/env

# Verificar instalação
uv --version
```

---

## 🚀 Instalação do Ambiente

### 1. Clonar o repositório

```bash
git clone https://github.com/<seu-usuario>/<seu-repo>.git
cd <seu-repo>
```

### 2. Criar o ambiente virtual com UV e instalar dependências

```bash
# Criar ambiente virtual com Python 3.11
uv venv --python 3.11

# Ativar o ambiente virtual
source .venv/bin/activate

# Instalar todas as dependências do projeto
uv sync
```

> **Nota:** O arquivo `pyproject.toml` já contém todas as dependências necessárias. O comando `uv sync` as instala automaticamente.

### 3. Dependências do projeto (`pyproject.toml`)

O arquivo `pyproject.toml` na raiz do projeto tem a seguinte estrutura:

```toml
[project]
name = "spark-lakehouse"
version = "0.1.0"
description = "Apache Spark com Delta Lake e Apache Iceberg"
requires-python = ">=3.11"

dependencies = [
    "pyspark==3.5.1",
    "delta-spark==3.2.0",
    "jupyterlab>=4.0.0",
    "ipykernel>=6.0.0",
    "pandas>=2.0.0",
    "pyarrow>=14.0.0",
    "mkdocs>=1.5.0",
    "mkdocs-material>=9.0.0",
]
```

### 4. Registrar o kernel do Jupyter

```bash
# Com o ambiente virtual ativo
python -m ipykernel install --user --name spark-lakehouse --display-name "Python (spark-lakehouse)"
```

---

## 📁 Estrutura do Projeto

```
spark-lakehouse/
│
├── notebooks/
│   ├── delta_lake.ipynb          # Notebook com Delta Lake
│   └── iceberg.ipynb             # Notebook com Apache Iceberg
│
├── docs/                         # Documentação MkDocs
│   ├── index.md                  # Contextualização do trabalho
│   ├── spark.md                  # Apache Spark / PySpark
│   ├── delta.md                  # Delta Lake
│   └── iceberg.md                # Apache Iceberg
│
├── data/                         # Dados gerados pelos notebooks (gitignore)
│   ├── delta/
│   └── iceberg/
│
├── pyproject.toml                # Dependências do projeto (UV)
├── mkdocs.yml                    # Configuração do MkDocs
└── README.md                     # Este arquivo
```

---

## ▶️ Como Executar os Notebooks

### Iniciar o JupyterLab

```bash
# Certifique-se de que o ambiente virtual está ativo
source .venv/bin/activate

# Iniciar o JupyterLab
jupyter lab
```

O JupyterLab abrirá automaticamente no navegador em `http://localhost:8888`.

### Selecionar o Kernel correto

Ao abrir um notebook, selecione o kernel **"Python (spark-lakehouse)"** para garantir que todas as dependências estão disponíveis.

### Ordem de execução recomendada

1. Abra `notebooks/delta_lake.ipynb` → execute célula por célula
2. Abra `notebooks/iceberg.ipynb` → execute célula por célula

> ⚠️ **Atenção:** Execute as células na ordem. A sessão Spark é criada na primeira célula e reutilizada nas demais.

---

## 📚 Documentação MkDocs

### Visualizar localmente

```bash
# Com o ambiente virtual ativo
mkdocs serve
```

Acesse em: `http://127.0.0.1:8000`

### Publicar no GitHub Pages

```bash
mkdocs gh-deploy
```

A documentação ficará disponível em: `https://<seu-usuario>.github.io/<seu-repo>/`

---

## 👥 Integrantes

| Nome | GitHub |
|---|---|
| João Pereira | [@joojpereira](https://github.com/joojpereira) |
| Bruno Sabino | [@SabinexX](https://github.com/SabinexX) |
| Filipe Jeremias | [@oEngenh3iro](https://github.com/oEngenh3iro) |

---

## 📄 Referências

- [Documentação Oficial Apache Spark](https://spark.apache.org/docs/latest/)
- [Documentação Delta Lake](https://docs.delta.io/latest/index.html)
- [Documentação Apache Iceberg](https://iceberg.apache.org/docs/latest/)
- [Canal DataWay BR - YouTube](https://www.youtube.com/@DataWayBR)
- [Repositório spark-delta](https://github.com/jlsilva01/spark-delta)
- [Repositório spark-iceberg](https://github.com/jlsilva01/spark-iceberg)
- [Documentação UV](https://docs.astral.sh/uv/)
