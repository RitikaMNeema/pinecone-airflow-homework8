# Medium to Pinecone Search Engine (Airflow + Sentence Transformers)

This project builds a **semantic search engine** using **Apache Airflow**, **Sentence Transformers**, and **Pinecone**.  
The DAG automates downloading Medium article data, generating embeddings, creating a Pinecone index, and running semantic search queries.

---

## Project Overview

### Objective
Build a vector search pipeline that:
1. Downloads and preprocesses a Medium dataset.
2. Generates embeddings using `sentence-transformers/all-MiniLM-L6-v2`.
3. Stores and queries embeddings in a **Pinecone** vector database.
4. Orchestrates all steps through **Apache Airflow**.

---

## Tech Stack

| Component | Purpose |
|------------|----------|
| **Apache Airflow** | DAG orchestration (ETL pipeline) |
| **Sentence Transformers** | Generate semantic embeddings |
| **Pinecone** | Vector database for similarity search |
| **Docker Compose** | Containerized Airflow environment |
| **Python / Pandas** | Data processing and I/O |

---

## Project Structure

your-project/
├── docker-compose.yaml # Airflow + Dependencies (includes Pinecone & Sentence Transformers)
├── dags/
│ └── Medium_to_Pinecone.py # Main Airflow DAG


---

## Airflow Variable Configuration

Add the following Variables in **Airflow UI → Admin → Variables**  
(or via CLI inside the webserver container):

| Key | Example Value |
|-----|----------------|
| `PINECONE_API_KEY` | your-real-pinecone-key |
| `PINECONE_INDEX_NAME` | semantic-search-fast |
| `PINECONE_REGION` | us-east-1 |
| `PINECONE_CLOUD` | aws |

**CLI Example:**
```bash
docker compose exec airflow-webserver airflow variables set PINECONE_API_KEY "<your-key>"
docker compose exec airflow-webserver airflow variables set PINECONE_INDEX_NAME "semantic-search-fast"
docker compose exec airflow-webserver airflow variables set PINECONE_REGION "us-east-1"
docker compose exec airflow-webserver airflow variables set PINECONE_CLOUD "aws" ```

**DAG Workflow**

download_data → Downloads Medium dataset (medium_data.csv)  
preprocess_data → Cleans and creates preprocessed CSV for embeddings  
create_pinecone_index → Creates or reuses a Pinecone index  
generate_embeddings_and_upsert → Generates embeddings and ingests them into Pinecone  
test_search_query → Runs a sample query: "what is ethics in AI"


**Setup Instructions**

# 1 Start Airflow
docker compose down
docker compose up -d --build

# 2 Access Airflow UI
# open: http://localhost:8080
# default creds: airflow / airflow

# 3 Trigger the DAG
docker compose exec airflow-webserver airflow dags trigger Medium_to_Pinecone

**Output Example**

Search results for query: 'what is ethics in AI'
ID: 102, Score: 0.8921, Title: AI and ethics in technology...
ID: 458, Score: 0.8803, Title: Exploring fairness in AI systems...
ID: 209, Score: 0.8739, Title: The role of ethics in modern AI...
