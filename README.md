# Big Data Technologies Project, Group 20: Yan Zheng, Fahimeh Gholami, Yilin Lai   

# Regional Economy Analysis with MongoDB and PySpark

This project **(bigdata.ipynb)** is divided into two parts and demonstrates a pipeline that loads regional economy data into MongoDB and performs data analysis using PySpark. The workflow includes data ingestion, indexing, Spark-MongoDB integration, performance tuning, and lays the foundation for visualization and insights.

---

## Project Overview

- **Part 1:** Use `pandas` and `pymongo` to load CSV data into MongoDB. Check if need data cleaning. (Fahimeh Gholami)
- **Part 2:** Use `PySpark` to read data from MongoDB, cache data, and optimize performance using Spark settings. (Yilin Lai)
- **Part 3:** Compare Performance between Spark SQL and DataFrame API. (Yan Zheng)

---

## Setup & Requirements

Install the required Python packages:

```bash
pip install pandas pymongo matplotlib pyspark
```

Ensure the following are installed and configured:

- **MongoDB** running on `localhost:27017`
- **Apache Spark**
- Java 8 or higher

---

## How to Run the Notebook

### 1. Start MongoDB

Ensure MongoDB is running locally:

```bash
mongod
```

---

### 2. Open and Run `bigdata.ipynb`

Run all cells in the notebook to execute both parts of the project:

#### Part 1: Data Ingestion (via Pandas and PyMongo)

- Loads data from:
    - `Finance.csv`
    - `Industry.csv`
    - `Educationv.csv`
- Prints missing values in each dataset
- Inserts the data into MongoDB collections:
    - `finance`
    - `industry`
    - `education`
- Creates index on `Year` field in all collections

#### Part 2: Spark Analysis (via PySpark and MongoDB Connector)

- Creates a SparkSession configured with MongoDB Spark Connector
- Loads collections as DataFrames
- Sets `spark.sql.shuffle.partitions` to 50 for performance optimization
- Caches `finance_df` to improve reusability

#### Part 3: Compare Performance between Spark SQL and DataFrame API

**Spark SQL vs DataFrame API Benchmark**

- Registers `finance_df` as a temp view and runs a SQL query to calculate the sum of `Less_than_5000` by year
- Compares the performance of the same aggregation using the DataFrame API

**Schema Check and Preprocessing**

- Displays schema and sample data from `industry_df` and `education_df`
- Converts specific finance columns to integers
- Fills missing values in `industry_df` and `education_df`

**Optimized Join Operation**

- Performs a broadcast join between `finance_df` and `education_df` on the `Year` column

**Aggregation & Visualization**

- Aggregates total income under $5000 by year using `groupBy().sum()`
- Converts the result to a pandas DataFrame
- Visualizes the trend using `matplotlib` (line plot)

**Complex Query with MongoDB Aggregation Pipeline**

- Uses a MongoDB aggregation pipeline directly within Spark to:
  - Filter documents for years >= 2019
  - Group and sum `Less_than_5000`
  - Sort and project relevant fields 

---

## MongoDB Integration

- Uses `pymongo` to connect to local MongoDB instance
- Clears existing documents before each insertion to avoid duplicates
- Inserts data using `insert_many()` from dictionaries
- Creates indexes on the `Year` field in each collection to improve query performance

---

## Spark Implementation Details

- Uses `pyspark.sql.SparkSession` to build a Spark context
- Loads data from MongoDB using `format("mongo")` with appropriate collection URIs
- Optimizes Spark by reducing shuffle partitions from 200 to 50
- Caches frequently used DataFrame for efficiency

---

## Clean Up

To clear all documents from the MongoDB database:

```python
# In a Python shell
from pymongo import MongoClient
client = MongoClient("mongodb://localhost:27017/")
db = client["regional_economy"]
db.finance.delete_many({})
db.industry.delete_many({})
db.education.delete_many({})
```


