### Pattern Matching in Cypher Query Language

Pattern matching is a core concept in Cypher, the query language for Neo4j. It allows you to describe graph patterns and find data that fits those patterns. Patterns are composed of nodes, relationships, and properties.

### Basic Pattern Matching Syntax

- **Node**: Represented by parentheses `(n)`. You can specify labels and properties.
  ```cypher
  (n:Label {property: 'value'})
  ```
- **Relationship**: Represented by brackets `[r:RELATIONSHIP_TYPE]` between nodes.
  ```cypher
  (a)-[r:RELATIONSHIP_TYPE]->(b)
  ```

### Northwind Database Overview

The Northwind database is a sample dataset containing information about customers, employees, orders, products, suppliers, and more.

### Examples Using the Northwind Database

#### 1. Match Nodes

**Example**: Find all customers.
```cypher
MATCH (c:Customer)
RETURN c
```

**Example**: Find all products.
```cypher
MATCH (p:Product)
RETURN p
```

#### 2. Match Nodes with Properties

**Example**: Find customers from Germany.
```cypher
MATCH (c:Customer {country: 'Germany'})
RETURN c
```

**Example**: Find products with a price greater than 20.
```cypher
MATCH (p:Product)
WHERE p.price > 20
RETURN p
```

#### 3. Match Nodes and Relationships

**Example**: Find all orders placed by customers.
```cypher
MATCH (c:Customer)-[r:PLACED]->(o:Order)
RETURN c, r, o
```

**Example**: Find employees who work in the "Sales" department.
```cypher
MATCH (e:Employee)-[:WORKS_IN]->(d:Department {name: 'Sales'})
RETURN e
```

#### 4. Using Variables

**Example**: Find customers and their orders.
```cypher
MATCH (c:Customer)-[:PLACED]->(o:Order)
RETURN c.customerID, c.contactName, o.orderID
```

#### 5. Matching Multiple Patterns

**Example**: Find customers, their orders, and the employees who handled those orders.
```cypher
MATCH (c:Customer)-[:PLACED]->(o:Order)<-[:HANDLED]-(e:Employee)
RETURN c.contactName, o.orderID, e.lastName
```

**Example**: Find products and their suppliers.
```cypher
MATCH (p:Product)<-[:SUPPLIES]-(s:Supplier)
RETURN p.productName, s.companyName
```

#### 6. Optional Matches

**Example**: Find customers and optionally their orders (including customers who have not placed any orders).
```cypher
MATCH (c:Customer)
OPTIONAL MATCH (c)-[:PLACED]->(o:Order)
RETURN c.contactName, o.orderID
```

#### 7. Filtering with `WHERE` Clause

**Example**: Find customers who placed orders after January 1, 2021.
```cypher
MATCH (c:Customer)-[:PLACED]->(o:Order)
WHERE o.orderDate > date('2021-01-01')
RETURN c.contactName, o.orderID
```

**Example**: Find products with a price between 10 and 30.
```cypher
MATCH (p:Product)
WHERE p.price >= 10 AND p.price <= 30
RETURN p.productName, p.price
```

#### 8. Using Aggregations

**Example**: Count the number of orders each customer has placed.
```cypher
MATCH (c:Customer)-[:PLACED]->(o:Order)
RETURN c.contactName, count(o) AS numberOfOrders
```

**Example**: Find the total value of orders placed by each customer.
```cypher
MATCH (c:Customer)-[:PLACED]->(o:Order)-[:CONTAINS]->(p:Product)
RETURN c.contactName, sum(p.price) AS totalValue
```

#### 9. Pattern Matching with Relationships

**Example**: Find customers who have ordered products supplied by a specific supplier.
```cypher
MATCH (c:Customer)-[:PLACED]->(o:Order)-[:CONTAINS]->(p:Product)<-[:SUPPLIES]-(s:Supplier {companyName: 'Exotic Liquids'})
RETURN c.contactName, p.productName, s.companyName
```

### String Matching and Regular Expressions

#### 10. `STARTS WITH`

**Example**: Find customers whose contact name starts with "Eliza".
```cypher
MATCH (c:Customer)
WHERE c.contactName STARTS WITH 'Eliza'
RETURN c
```

#### 11. `ENDS WITH`

**Example**: Find employees whose last name ends with "son".
```cypher
MATCH (e:Employee)
WHERE e.lastName ENDS WITH 'son'
RETURN e
```

#### 12. `CONTAINS`

**Example**: Find products that contain "Chai" in their name.
```cypher
MATCH (p:Product)
WHERE p.productName CONTAINS 'Chai'
RETURN p
```

#### 13. Regular Expressions

**Example**: Find customers whose contact name matches the regular expression for names starting with "A" and ending with "s".
```cypher
MATCH (c:Customer)
WHERE c.contactName =~ 'A.*s'
RETURN c
```

### Advanced Pattern Matching

#### 14. Using `WITH` for Intermediate Results

**Example**: Find the top 5 customers by the number of orders placed.
```cypher
MATCH (c:Customer)-[:PLACED]->(o:Order)
WITH c, count(o) AS numberOfOrders
ORDER BY numberOfOrders DESC
LIMIT 5
RETURN c.contactName, numberOfOrders
```

#### 15. Path Patterns

**Example**: Find all paths from customers to products they ordered.
```cypher
MATCH path = (c:Customer)-[:PLACED]->(o:Order)-[:CONTAINS]->(p:Product)
RETURN path
```
