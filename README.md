# 📝 Assignment: Database Design and Normalization

## 🎯 **Learning Objectives**
* 🛠️ **Understand the principles of good database design** and **normalization**.
* 💡 **Apply normalization techniques** to improve database structure and efficiency.
* 🔍 **Learn First, Second, and Third Normal Forms** (1NF, 2NF, 3NF) to eliminate redundancy and optimize data storage.

---

## 📋 **What You'll Need**
* 💻 A computer with internet access.
* ✍️ A code editor (e.g., Visual Studio Code).
* 🖥️ MySQL Workbench or another SQL database environment.

---


## 📝 Submission Instructions  
📂 Write all your SQL queries in the **answers.sql** file.  
✍️ Answer each question concisely and make sure your queries are clear and correct.  
🗣️ Structure your responses clearly, and use comments if necessary to explain your approach.

--- 

## 📚 Assignment Questions

---

### Question 1 Achieving 1NF (First Normal Form) 🛠️
Task:
- You are given the following table **ProductDetail**:

| OrderID | CustomerName  | Products                        |
|---------|---------------|---------------------------------|
| 101     | John Doe      | Laptop, Mouse                   |
| 102     | Jane Smith    | Tablet, Keyboard, Mouse         |
| 103     | Emily Clark   | Phone                           |


- In the table above, the **Products column** contains multiple values, which violates **1NF**.
- **Write an SQL query** to transform this table into **1NF**, ensuring that each row represents a single product for an order

--- SELECT OrderID, CustomerName, TRIM(SUBSTRING_INDEX(SUBSTRING_INDEX(Products, ',', n.n), ',', -1)) AS Product
FROM ProductDetail
JOIN (SELECT 1 AS n UNION ALL SELECT 2 UNION ALL SELECT 3 UNION ALL SELECT 4 UNION ALL SELECT 5) n
  ON CHAR_LENGTH(Products) - CHAR_LENGTH(REPLACE(Products, ',', '')) >= n.n - 1
ORDER BY OrderID, n.n;


### Question 2 Achieving 2NF (Second Normal Form) 🧩

- You are given the following table **OrderDetails**, which is already in **1NF** but still contains partial dependencies:

| OrderID | CustomerName  | Product      | Quantity |
|---------|---------------|--------------|----------|
| 101     | John Doe      | Laptop       | 2        |
| 101     | John Doe      | Mouse        | 1        |
| 102     | Jane Smith    | Tablet       | 3        |
| 102     | Jane Smith    | Keyboard     | 1        |
| 102     | Jane Smith    | Mouse        | 2        |
| 103     | Emily Clark   | Phone        | 1        |

- In the table above, the **CustomerName** column depends on **OrderID** (a partial dependency), which violates **2NF**. 

- Write an SQL query to transform this table into **2NF** by removing partial dependencies. Ensure that each non-key column fully depends on the entire primary key.
To transform the **OrderDetails** table into **Second Normal Form (2NF)**, we need to remove **partial dependencies**—where non-key columns depend on only part of the primary key. In this case, the **CustomerName** depends only on **OrderID**, not on the entire composite key (**OrderID, Product**). This violates **2NF**, and we need to create two separate tables to resolve this issue.

### Steps to achieve **2NF**:
1. **Separate** the **CustomerName** into its own table to avoid the partial dependency on **OrderID**.
2. The **OrderDetails** table will contain only information related to **OrderID**, **Product**, and **Quantity**.

### Solution:

1. **Create a new table** for the **customers** that stores **OrderID** and **CustomerName**.
2. **Modify** the **OrderDetails** table to remove **CustomerName**.

### SQL Queries:

#### 1. Create the **Customers** table:
```sql
CREATE TABLE Customers (
    OrderID INT PRIMARY KEY,
    CustomerName VARCHAR(255)
);
```

#### 2. Insert the relevant data into the **Customers** table:
```sql
INSERT INTO Customers (OrderID, CustomerName)
SELECT DISTINCT OrderID, CustomerName
FROM OrderDetails;
```

#### 3. Modify the **OrderDetails** table to remove **CustomerName**:
```sql
CREATE TABLE OrderDetails_2NF (
    OrderID INT,
    Product VARCHAR(255),
    Quantity INT,
    PRIMARY KEY (OrderID, Product),
    FOREIGN KEY (OrderID) REFERENCES Customers(OrderID)
);
```

#### 4. Insert the relevant data into the **OrderDetails_2NF** table:
```sql
INSERT INTO OrderDetails_2NF (OrderID, Product, Quantity)
SELECT OrderID, Product, Quantity
FROM OrderDetails;
```

### Resulting Structure:

#### **Customers Table**:
| OrderID | CustomerName  |
|---------|---------------|
| 101     | John Doe      |
| 102     | Jane Smith    |
| 103     | Emily Clark   |

#### **OrderDetails_2NF Table**:
| OrderID | Product      | Quantity |
|---------|--------------|----------|
| 101     | Laptop       | 2        |
| 101     | Mouse        | 1        |
| 102     | Tablet       | 3        |
| 102     | Keyboard     | 1        |
| 102     | Mouse        | 2        |
| 103     | Phone        | 1        |

### Explanation:
- The **Customers** table now contains **OrderID** and **CustomerName**, removing the partial dependency on **OrderID** in the **OrderDetails** table.
- The **OrderDetails_2NF** table only contains **OrderID**, **Product**, and **Quantity**, with the composite key being **OrderID + Product**.
- We’ve also set up a **foreign key relationship** between **OrderDetails_2NF** and the **Customers** table based on **OrderID**.

This structure ensures that all non-key columns (like **CustomerName**) fully depend on the primary key (**OrderID**), thus achieving **2NF**.

---
Good luck 🚀
