### Aggregation and Functions in Neo4j with Cypher

Neo4j provides powerful aggregation and functions to manipulate and analyze graph data.


### Basic Aggregation Functions

#### 1. **COUNT**
The `COUNT` function returns the number of nodes, relationships, or paths.

**Example:** Count the number of products in the database.
```cypher
MATCH (p:Product)
RETURN COUNT(p) AS product_count;
```

#### 2. **SUM**
The `SUM` function returns the sum of numeric values.

**Example:** Sum the quantities of all products in orders.
```cypher
MATCH (o:Order)-[r:ORDERS]->(p:Product)
RETURN SUM(r.quantity) AS total_quantity;
```

#### 3. **AVG**
The `AVG` function returns the average of numeric values.

**Example:** Average price of products.
```cypher
MATCH (p:Product)
RETURN AVG(p.unitPrice) AS average_price;
```

#### 4. **MIN and MAX**
The `MIN` and `MAX` functions return the minimum and maximum values, respectively.

**Example:** Find the cheapest and most expensive product.
```cypher
MATCH (p:Product)
RETURN MIN(p.unitPrice) AS cheapest_product, MAX(p.unitPrice) AS most_expensive_product;
```

### Grouping with Aggregation


**Example:** Total sales per product category.
```cypher
MATCH (p:Product)-[:PART_OF]->(c:Category), (o:Order)-[r:ORDERS]->(p)
RETURN c.categoryName AS category, SUM(r.quantity * p.unitPrice) AS total_sales
ORDER BY total_sales DESC;
```

### Using Functions

#### 1. **String Functions**

**Example:** Convert product names to uppercase using the `toUpper` function.
```cypher
MATCH (p:Product)
RETURN p.productName, toUpper(p.productName) AS uppercased_name;
```

#### 2. **Mathematical Functions**

**Example:** Calculate the discounted price of products.
```cypher
MATCH (p:Product)
RETURN p.productName, p.unitPrice, p.unitPrice * 0.9 AS discounted_price;
```

#### 3. **Date Functions**

Neo4j has various date functions to manipulate and format dates.

**Example:** Calculate the number of days between order date and required date.
```cypher
MATCH (o:Order)
WITH o,
     SUBSTRING(o.orderDate, 0, 10) AS orderDateFormatted,
     SUBSTRING(o.requiredDate, 0, 10) AS requiredDateFormatted
RETURN o.orderDate, o.requiredDate, 
       duration.inDays(
           date(orderDateFormatted), 
           date(requiredDateFormatted)
       ).days AS days_difference;
```

### Advanced Aggregation: Collecting Data

The `COLLECT` function is used to aggregate elements into a list.

**Example:** Collect all products in each category.
```cypher
MATCH (c:Category)<-[:PART_OF]-(p:Product)
RETURN c.categoryName AS category, COLLECT(p.productName) AS products;
```

### Some other Examples

#### Example 1: Top 5 Customers by Order Value
```cypher
MATCH (c:Customer)-[:PURCHASED]->(o:Order)-[r:ORDERS]->(p:Product)
WITH c, SUM(r.quantity * p.unitPrice) AS order_value
RETURN c.companyName AS customer, order_value
ORDER BY order_value DESC
LIMIT 5;
```

#### Example 2: Monthly Sales in the Last Year
```cypher
MATCH (o:Order)-[r:ORDERS]->(p:Product)
WHERE o.orderDate >= '2023-05-01' AND o.orderDate < '2024-05-01'
WITH date.truncate('month', date(SUBSTRING(o.orderDate, 0, 10))) AS month, SUM(r.quantity * p.unitPrice) AS monthly_sales
RETURN month, monthly_sales
ORDER BY month;
```

These examples provide a basic understanding of how to use aggregation and functions in Neo4j with Cypher queries. Feel free to expand on these queries and explore more functions as needed for your specific use cases.

-----------------------

 Below is a list of these functions along with examples for each.

#### 1. String Functions

- **toUpper()**: Converts a string to uppercase.
  ```cypher
  MATCH (p:Product)
  RETURN p.productName, toUpper(p.productName) AS uppercased_name;
  ```

- **toLower()**: Converts a string to lowercase.
  ```cypher
  MATCH (p:Product)
  RETURN p.productName, toLower(p.productName) AS lowercased_name;
  ```

- **substring()**: Extracts a substring from a string.
  ```cypher
  MATCH (p:Product)
  RETURN p.productName, substring(p.productName, 0, 5) AS short_name;
  ```

- **replace()**: Replaces part of a string with another string.
  ```cypher
  MATCH (p:Product)
  RETURN p.productName, replace(p.productName, " ", "_") AS formatted_name;
  ```

- **trim()**: Removes leading and trailing whitespace from a string.
  ```cypher
  MATCH (p:Product)
  RETURN p.productName, trim("  " + p.productName + "  ") AS trimmed_name;
  ```

#### 2. Numeric Functions

- **abs()**: Returns the absolute value of a number.
  ```cypher
  RETURN abs(-5) AS absolute_value;
  ```

- **round()**: Rounds a number to the nearest integer.
  ```cypher
  RETURN round(3.14159) AS rounded_value;
  ```

- **ceil()**: Rounds a number up to the nearest integer.
  ```cypher
  RETURN ceil(3.14159) AS ceiling_value;
  ```

- **floor()**: Rounds a number down to the nearest integer.
  ```cypher
  RETURN floor(3.14159) AS floor_value;
  ```

- **rand()**: Returns a random number between 0 and 1.
  ```cypher
  RETURN rand() AS random_value;
  ```

#### 3. Date Functions

- **date()**: Returns the current date or creates a date from a string.
  ```cypher
  RETURN date() AS current_date;
  ```

- **datetime()**: Returns the current date and time.
  ```cypher
  RETURN datetime() AS current_datetime;
  ```

- **date.truncate()**: Truncates a date to a specific unit (e.g., month).
  ```cypher
  RETURN date.truncate('month', date("2023-05-16")) AS truncated_date;
  ```

#### 4. Aggregation Functions

- **COUNT()**: Counts the number of nodes, relationships, or paths.
  ```cypher
  MATCH (p:Product)
  RETURN COUNT(p) AS product_count;
  ```

- **SUM()**: Returns the sum of numeric values.
  ```cypher
  MATCH (o:Order)-[r:ORDERS]->(p:Product)
  RETURN SUM(r.quantity) AS total_quantity;
  ```

- **AVG()**: Returns the average of numeric values.
  ```cypher
  MATCH (p:Product)
  RETURN AVG(p.unitPrice) AS average_price;
  ```

- **MIN()**: Returns the minimum value.
  ```cypher
  MATCH (p:Product)
  RETURN MIN(p.unitPrice) AS cheapest_product;
  ```

- **MAX()**: Returns the maximum value.
  ```cypher
  MATCH (p:Product)
  RETURN MAX(p.unitPrice) AS most_expensive_product;
  ```

- **COLLECT()**: Aggregates elements into a list.
  ```cypher
  MATCH (c:Category)<-[:PART_OF]-(p:Product)
  RETURN c.categoryName AS category, COLLECT(p.productName) AS products;
  ```

#### 5. List Functions

- **size()**: Returns the size of a list.
  ```cypher
  MATCH (c:Category)<-[:PART_OF]-(p:Product)
  RETURN c.categoryName AS category, size(COLLECT(p.productName)) AS product_count;
  ```

- **head()**: Returns the first element of a list.
  ```cypher
  MATCH (c:Category)<-[:PART_OF]-(p:Product)
  WITH c, COLLECT(p.productName) AS products
  RETURN c.categoryName AS category, head(products) AS first_product;
  ```

- **last()**: Returns the last element of a list.
  ```cypher
  MATCH (c:Category)<-[:PART_OF]-(p:Product)
  WITH c, COLLECT(p.productName) AS products
  RETURN c.categoryName AS category, last(products) AS last_product;
  ```

- **range()**: Creates a list of numbers in a specified range.
  ```cypher
  RETURN range(0, 10) AS number_range;
  ```
