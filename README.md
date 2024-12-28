# SQL Basics and Advanced Concepts for AI Engineers

## **1. Basics of Relational Databases**

### **What is a Relational Database?**
- A relational database organizes data into tables with rows and columns.
- Key terms:
  - **Tables**: Collections of data, like spreadsheets.
  - **Primary Key**: A unique identifier for each record.
  - **Foreign Key**: A field linking two tables.

### **Example**
**Library Database**
- **Books Table**: Contains book details (`BookID`, `Title`, `Author`).
- **Members Table**: Contains member details (`MemberID`, `Name`).
- **BorrowedBooks Table**: Links members and books using `MemberID` and `BookID`.

---

## **2. Basic Queries**

### **SELECT: Fetch data from a table**
```sql
SELECT * FROM Books; -- Fetch all rows and columns
SELECT Title, Author FROM Books; -- Fetch specific columns
```

### **WHERE: Filter rows based on conditions**
```sql
SELECT * FROM Books WHERE Author = 'J.K. Rowling';
```

### **LIKE: Pattern matching**
```sql
SELECT * FROM Books WHERE Title LIKE '%Harry%'; -- Any title containing 'Harry'
```

### **DISTINCT: Remove duplicates**
```sql
SELECT DISTINCT Author FROM Books; -- Unique authors
```

### **BETWEEN: Range filtering**
```sql
SELECT * FROM Books WHERE BookID BETWEEN 10 AND 20;
```

### **GROUP BY and Aggregates: Organize and summarize data**
```sql
SELECT Author, COUNT(*) AS TotalBooks FROM Books GROUP BY Author;
```

### **ORDER BY: Sort results**
```sql
SELECT * FROM Books ORDER BY Title ASC; -- Alphabetical order
```

---

## **3. Advanced Queries**

### **Common Table Expressions (CTE): Temporary table for readability**
```sql
WITH PopularBooks AS (
    SELECT Title, COUNT(*) AS BorrowedCount
    FROM BorrowedBooks
    GROUP BY Title
)
SELECT * FROM PopularBooks WHERE BorrowedCount > 5;
```

### **Subqueries: Queries inside a query**
```sql
SELECT * FROM Books WHERE BookID IN (
    SELECT BookID FROM BorrowedBooks WHERE MemberID = 1
);
```

### **Window Functions: Perform calculations over a set of rows**
```sql
SELECT Title, Author, RANK() OVER (PARTITION BY Author ORDER BY Title) AS Rank
FROM Books;
```

---

## **4. Joins**

### **INNER JOIN: Combine matching rows from two tables**
```sql
SELECT Members.Name, Books.Title
FROM BorrowedBooks
INNER JOIN Members ON BorrowedBooks.MemberID = Members.MemberID
INNER JOIN Books ON BorrowedBooks.BookID = Books.BookID;
```

### **LEFT JOIN: Include all rows from the left table and matching rows from the right**
```sql
SELECT Members.Name, Books.Title
FROM Members
LEFT JOIN BorrowedBooks ON Members.MemberID = BorrowedBooks.MemberID
LEFT JOIN Books ON BorrowedBooks.BookID = Books.BookID;
```

### **RIGHT JOIN and FULL JOIN: Similar concepts**
```sql
SELECT Members.Name, Books.Title
FROM BorrowedBooks
RIGHT JOIN Members ON BorrowedBooks.MemberID = Members.MemberID;

SELECT Members.Name, Books.Title
FROM Members
FULL JOIN BorrowedBooks ON Members.MemberID = BorrowedBooks.MemberID;
```

---

## **5. Database Creation, Indexes, Stored Procedures**

### **Create a Database and Table**
```sql
CREATE DATABASE LibraryDB;
USE LibraryDB;

CREATE TABLE Books (
    BookID INT PRIMARY KEY,
    Title VARCHAR(100),
    Author VARCHAR(100)
);
```

### **Indexes: Speed up queries**
```sql
CREATE INDEX idx_author ON Books (Author);
```

### **Stored Procedures: Predefined SQL scripts**
```sql
CREATE PROCEDURE GetBooksByAuthor(IN authorName VARCHAR(100))
BEGIN
    SELECT * FROM Books WHERE Author = authorName;
END;

CALL GetBooksByAuthor('J.K. Rowling');
```

---

## **6. Additional Concepts for AI Engineers**

### **Normalization and Denormalization**
- **Normalization**: Organizing data to reduce redundancy.
- **Denormalization**: Opposite of normalization, useful for faster querying.

### **Handling Large Data**
- Techniques like partitioning, clustering, and using `EXPLAIN` to optimize queries.

### **Working with JSON**
```sql
SELECT JSON_EXTRACT(data, '$.field') AS ExtractedField FROM Logs;
```

### **Database Connections in Python**
```python
import sqlite3

connection = sqlite3.connect("library.db")
cursor = connection.cursor()
cursor.execute("SELECT * FROM Books")
for row in cursor.fetchall():
    print(row)
connection.close()
```

### **Extracting Data for Machine Learning**
```sql
SELECT MemberID, COUNT(BookID) AS BooksBorrowed FROM BorrowedBooks GROUP BY MemberID;
```

---

## **Future Topics for AI Engineers**
- Advanced query optimization techniques.
- Big data handling with SQL-based tools like Hive or Spark SQL.
- Integration with ML pipelines using tools like TensorFlow or PyTorch.


---

## **Future Topics for AI Engineers**

### **1. Advanced Query Optimization Techniques**

As datasets grow, query performance becomes crucial. Optimization ensures queries execute faster and more efficiently.

#### **a. Using `EXPLAIN` and `ANALYZE`**
- These commands provide insights into query execution plans, helping you identify performance bottlenecks.
  
```sql
EXPLAIN SELECT * FROM Books WHERE Author = 'J.K. Rowling';
```

- **Output Interpretation**:
  - **Index Usage**: Ensures indexes are being used.
  - **Cost Estimates**: Lower costs indicate more efficient queries.

#### **b. Avoid SELECT \*** 
Fetching unnecessary columns increases memory usage.
```sql
SELECT Title, Author FROM Books; -- Fetch only required columns
```

#### **c. Optimize Joins**
- Use indexes on columns involved in joins.
```sql
CREATE INDEX idx_memberID ON BorrowedBooks (MemberID);
```
- Filter data early:
```sql
SELECT Members.Name, Books.Title
FROM BorrowedBooks
INNER JOIN Members ON BorrowedBooks.MemberID = Members.MemberID
WHERE BorrowedBooks.Date > '2023-01-01';
```

#### **d. Partitioning**
Divide large tables into smaller, manageable segments (e.g., by date).
```sql
CREATE TABLE BorrowedBooks_2023 PARTITION OF BorrowedBooks FOR VALUES FROM ('2023-01-01') TO ('2024-01-01');
```

---

### **2. Big Data Handling with SQL-Based Tools**

When data grows beyond traditional SQL's capacity, distributed systems like Hive and Spark SQL come into play.

#### **a. Apache Hive**
- SQL-like querying on distributed data stored in Hadoop.
  
**Example**: Query a large dataset of transactions.
```sql
CREATE EXTERNAL TABLE transactions (
    transaction_id STRING,
    amount FLOAT,
    date STRING
) 
STORED AS PARQUET;

SELECT date, SUM(amount) AS total_sales FROM transactions GROUP BY date;
```

#### **b. Apache Spark SQL**
- A fast, distributed query engine for big data.
  
**Python + Spark SQL Example**:
```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("BigDataSQL").getOrCreate()

# Load data into a DataFrame
df = spark.read.csv("transactions.csv", header=True)

# Register DataFrame as a SQL temporary view
df.createOrReplaceTempView("transactions")

# Run SQL query
spark.sql("SELECT date, SUM(amount) AS total_sales FROM transactions GROUP BY date").show()
```

#### **c. SQL-on-NoSQL**
Use SQL interfaces for NoSQL databases (e.g., Amazon Athena for S3).
```sql
SELECT * FROM s3://mybucket/transactions WHERE amount > 100;
```

---

### **3. Integration with ML Pipelines**

SQL is often used for feature engineering and preprocessing in ML pipelines.

#### **a. Feature Extraction with SQL**
Extract features directly in SQL for use in ML models.
```sql
SELECT 
    MemberID,
    COUNT(BookID) AS BorrowedBooks,
    AVG(DATEDIFF(ReturnDate, BorrowDate)) AS AvgBorrowDuration
FROM BorrowedBooks
GROUP BY MemberID;
```

#### **b. Using SQL with TensorFlow**
- Combine SQL data preprocessing with ML training.

**Example**: Load SQL data into a TensorFlow pipeline.
```python
import tensorflow as tf
import sqlite3
import pandas as pd

# Fetch data from SQL
connection = sqlite3.connect("library.db")
query = "SELECT MemberID, BorrowedBooks, AvgBorrowDuration FROM BorrowedFeatures;"
df = pd.read_sql_query(query, connection)

# Convert to TensorFlow dataset
dataset = tf.data.Dataset.from_tensor_slices((df[['BorrowedBooks', 'AvgBorrowDuration']].values, df['MemberID'].values))

# Build and train a model
model = tf.keras.Sequential([
    tf.keras.layers.Dense(10, activation='relu'),
    tf.keras.layers.Dense(1)
])

model.compile(optimizer='adam', loss='mse')
model.fit(dataset.batch(32), epochs=10)
```

#### **c. PyTorch Integration with SQL**
- Use SQL for feature preprocessing, then pass to PyTorch.

**Example**:
```python
import sqlite3
import torch
import pandas as pd

# Fetch data
connection = sqlite3.connect("library.db")
query = "SELECT BorrowedBooks, AvgBorrowDuration FROM BorrowedFeatures;"
data = pd.read_sql_query(query, connection)

# Convert to PyTorch tensors
features = torch.tensor(data.values, dtype=torch.float32)

# Define a simple PyTorch model
model = torch.nn.Sequential(
    torch.nn.Linear(features.shape[1], 10),
    torch.nn.ReLU(),
    torch.nn.Linear(10, 1)
)

# Forward pass
output = model(features)
print(output)
```

---

### **4. Advanced Use Cases**

#### **a. Building a Recommendation System**
SQL can preprocess data for collaborative filtering or content-based filtering.
```sql
SELECT MemberID, BookID, COUNT(*) AS BorrowCount
FROM BorrowedBooks
GROUP BY MemberID, BookID;
```

#### **b. Time-Series Analysis**
Use SQL to generate lagged features for time-series models.
```sql
SELECT 
    BookID, 
    BorrowDate, 
    LAG(BorrowDate) OVER (PARTITION BY BookID ORDER BY BorrowDate) AS PreviousBorrowDate
FROM BorrowedBooks;
```
