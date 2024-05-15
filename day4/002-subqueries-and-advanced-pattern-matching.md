### Subqueries in Cypher

Cypher supports subqueries, which allow you to encapsulate and reuse complex query logic. Subqueries enable you to perform additional processing on intermediate results and are particularly useful for tasks that require multiple steps or when you want to isolate parts of a query.

### Basic Subquery Syntax

Subqueries are enclosed within `{ }` and can be used in `CALL` and `WITH` clauses. Here are a few examples demonstrating different use cases of subqueries in Cypher.

### Example 1: Filtering and Aggregation with Subqueries

Suppose you want to find the top 5 customers by order value, but only consider orders placed between 1996 and 1998.

```cypher
MATCH (c:Customer)-[:PURCHASED]->(o:Order)
WHERE date(SUBSTRING(o.orderDate, 0, 10)) >= date('1996-01-01') AND date(SUBSTRING(o.orderDate, 0, 10)) <= date('1998-12-31')
WITH c, o
CALL {
    WITH o
    MATCH (o)-[r:ORDERS]->(p:Product)
    RETURN SUM(r.quantity * p.unitPrice) AS order_value
}
WITH c, order_value
RETURN c.companyName AS customer, order_value
ORDER BY order_value DESC
LIMIT 5;
```

### Example 2: Using Subqueries for Complex Calculations

You can use subqueries to calculate intermediate values, such as the average order value for each customer.

```cypher
MATCH (c:Customer)-[:PURCHASED]->(o:Order)
WHERE date(SUBSTRING(o.orderDate, 0, 10)) >= date('1996-01-01') AND date(SUBSTRING(o.orderDate, 0, 10)) <= date('1998-12-31')
WITH c
CALL {
    WITH c
    MATCH (c)-[:PURCHASED]->(o:Order)-[r:ORDERS]->(p:Product)
    RETURN AVG(r.quantity * p.unitPrice) AS avg_order_value
}
RETURN c.companyName AS customer, avg_order_value
ORDER BY avg_order_value DESC;
```

### Example 3: Nested Subqueries

You can nest subqueries to perform multiple levels of processing.

```cypher
MATCH (c:Customer)-[:PURCHASED]->(o:Order)
WHERE date(SUBSTRING(o.orderDate, 0, 10)) >= date('1996-01-01') AND date(SUBSTRING(o.orderDate, 0, 10)) <= date('1998-12-31')
WITH c, o
CALL {
    WITH o
    MATCH (o)-[r:ORDERS]->(p:Product)
    WITH p
    MATCH (p)-[:PART_OF]->(cat:Category)
    RETURN cat.categoryName AS category
}
WITH c, category
CALL {
    WITH c, category
    MATCH (c)-[:PURCHASED]->(o:Order)-[r:ORDERS]->(p:Product)-[:PART_OF]->(cat:Category)
    WHERE cat.categoryName = category
    RETURN SUM(r.quantity * p.unitPrice) AS category_order_value
}
RETURN c.companyName AS customer, category, category_order_value
ORDER BY category_order_value DESC;
```

### Example 4: Subqueries for Complex Path Queries

Subqueries can be used to simplify complex path queries by breaking them into manageable parts.

```cypher
MATCH (s:Supplier)
WITH s
CALL {
    WITH s
    MATCH (s)-[:SUPPLIES]->(p:Product)
    WITH p
    MATCH (p)-[:PART_OF]->(c:Category)
    RETURN p, c
}
RETURN s.companyName AS supplier, c.categoryName AS category, COUNT(p) AS product_count
ORDER BY product_count DESC;
```

### Benefits of Using Subqueries

1. **Modularity**: Subqueries help modularize complex queries, making them easier to read and maintain.
2. **Reusability**: You can reuse subqueries within a larger query to avoid redundancy.
3. **Isolation**: Subqueries isolate intermediate calculations, reducing the risk of errors in complex queries.
