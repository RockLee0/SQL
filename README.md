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


