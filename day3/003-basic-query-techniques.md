### Basic Query Techniques in Cypher for Clean and Optimized Code

To write clean and optimized Cypher queries, it’s essential to use best practices and techniques that enhance readability, maintainability, and performance. 

### 1. Use Proper Indexes

Indexes can significantly speed up queries. Create indexes on properties that are frequently used in `MATCH` or `WHERE` clauses.

**Example**: Create an index on `customerID` for the `Customer` nodes.
```cypher
CREATE INDEX FOR (c:Customer) ON (c.customerID)
```

### 2. Use Parameters

Parameters help in writing reusable and safe queries by separating the query logic from the data.

**Example**: Find a customer by `customerID` using a parameter.
```cypher
:param customerID => 'ALFKI';

MATCH (c:Customer {customerID: $customerID})
RETURN c
```

### 3. Limit the Scope of Matches

Limit the amount of data processed by the query to avoid unnecessary computations.

**Example**: Find the first 10 customers from Germany.
```cypher
MATCH (c:Customer {country: 'Germany'})
RETURN c
LIMIT 10
```

### 4. Use `WITH` for Intermediate Results

The `WITH` clause allows you to chain multiple parts of a query together and handle intermediate results.

**Example**: Find customers and their orders, but only return orders placed after January 1, 2021.
```cypher
MATCH (c:Customer)-[:PLACED]->(o:Order)
WHERE o.orderDate > date('2021-01-01')
WITH c, o
RETURN c.contactName, o.orderID, o.orderDate
```

### 5. Filter Early

Apply filters as early as possible in the query to reduce the amount of data processed in subsequent steps.

**Example**: Find employees in the "Sales" department and return their last names.
```cypher
MATCH (e:Employee)-[:WORKS_IN]->(d:Department {name: 'Sales'})
RETURN e.lastName
```

### 6. Use List Comprehensions

List comprehensions can simplify the processing and filtering of collections.

**Example**: Get the names of products that contain "Chai".
```cypher
MATCH (p:Product)
WHERE p.productName CONTAINS 'Chai'
RETURN collect(p.productName) AS chaiProducts
```

### 7. Avoid Cartesian Products

Cartesian products (cross joins) can lead to inefficient queries. Use `WITH` to segment query parts and avoid unintended cross joins.

**Example**: Find customers and their orders without producing a Cartesian product.
```cypher
MATCH (c:Customer)
WITH c
MATCH (c)-[:PLACED]->(o:Order)
RETURN c.customerID, o.orderID
```

### 8. Use Pattern Comprehensions

Pattern comprehensions allow you to return a list of results from a subquery pattern within a single query.

**Example**: Find customers and a list of their order IDs.
```cypher
MATCH (c:Customer)
RETURN c.customerID, [(c)-[:PLACED]->(o:Order) | o.orderID] AS orders
```

### 9. Aggregate Functions

Use aggregate functions to summarize data efficiently.

**Example**: Count the number of orders placed by each customer.
```cypher
MATCH (c:Customer)-[:PLACED]->(o:Order)
RETURN c.customerID, count(o) AS numberOfOrders
```

### 10. Profile and Explain

Use `PROFILE` and `EXPLAIN` to analyze the performance of your queries.

**Example**: Profile a query to see its execution plan.
```cypher
PROFILE
MATCH (c:Customer {country: 'Germany'})
RETURN c
```

### Examples with Northwind Database

#### Example 1: Find Customers with Orders Placed After a Certain Date

```cypher
:param orderDate => '2021-01-01';

MATCH (c:Customer)-[:PLACED]->(o:Order)
WHERE o.orderDate > date($orderDate)
RETURN c.contactName, o.orderID, o.orderDate
```

#### Example 2: Find Top 5 Products by Number of Orders

```cypher
MATCH (p:Product)<-[:CONTAINS]-(o:Order)
WITH p, count(o) AS orderCount
ORDER BY orderCount DESC
LIMIT 5
RETURN p.productName, orderCount
```

#### Example 3: Find Employees and Their Departments

```cypher
MATCH (e:Employee)-[:WORKS_IN]->(d:Department)
RETURN e.lastName, d.name
```
