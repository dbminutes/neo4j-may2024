### 1. Create Nodes and Relationships

```cypher
UNWIND range(1, 1000) AS id
CREATE (p:Person {id: id, name: 'Person ' + id, age: toInteger(rand() * 100)})
WITH p
MATCH (c:City)
WHERE rand() < 0.1
CREATE (p)-[:LIVES_IN]->(c)
RETURN p;
```

### 2. Update Nodes

```cypher
UNWIND range(1, 1000) AS id
MATCH (p:Person {id: id})
SET p.age = p.age + 1
RETURN p;
```

### 3. Read Operations

```cypher
MATCH (p:Person)
RETURN p.name, p.age
ORDER BY p.age DESC
LIMIT 100;
```

### 4. Complex Read and Write

```cypher
MATCH (p:Person)
WHERE p.age > 50
WITH p
LIMIT 100
SET p.retired = true
RETURN p;
```

### 5. Create Relationships Between Existing Nodes

```cypher
MATCH (p1:Person), (p2:Person)
WHERE p1 <> p2 AND rand() < 0.01
CREATE (p1)-[:FRIENDS_WITH]->(p2)
RETURN p1, p2;
```

### 6. Delete Nodes and Relationships

```cypher
MATCH (p:Person)
WHERE p.age < 20
WITH p
LIMIT 100
DETACH DELETE p;
```

### 7. Using APOC Procedures for Bulk Operations

#### Create Large Number of Nodes

```cypher
CALL apoc.periodic.iterate(
  "UNWIND range(1, 100000) AS id RETURN id",
  "CREATE (p:Person {id: id, name: 'Person ' + id, age: toInteger(rand() * 100)})",
  {batchSize: 1000, parallel: true}
);
```

#### Create Relationships in Batches

```cypher
CALL apoc.periodic.iterate(
  "MATCH (p1:Person), (p2:Person) WHERE p1 <> p2 RETURN p1, p2",
  "CREATE (p1)-[:KNOWS]->(p2)",
  {batchSize: 1000, parallel: true}
);
```

### 8. Combining Operations

```cypher
CALL apoc.periodic.iterate(
  "MATCH (p:Person) RETURN p",
  "WITH p
   SET p.visited = coalesce(p.visited, 0) + 1
   RETURN p",
  {batchSize: 100, parallel: true}
);
```

### Running Scripts

You can run these scripts using Neo4j Desktop's "Run Query" feature or create a `.cypher` script file and use the `cypher-shell` command to execute them from the command line.

#### Example Cypher Script (`load-test.cypher`)

```cypher
UNWIND range(1, 1000) AS id
CREATE (p:Person {id: id, name: 'Person ' + id, age: toInteger(rand() * 100)});

UNWIND range(1, 1000) AS id
MATCH (p:Person {id: id})
SET p.age = p.age + 1;

MATCH (p:Person)
RETURN p.name, p.age
ORDER BY p.age DESC
LIMIT 100;

MATCH (p:Person)
WHERE p.age > 50
WITH p
LIMIT 100
SET p.retired = true
RETURN p;

MATCH (p1:Person), (p2:Person)
WHERE p1 <> p2 AND rand() < 0.01
CREATE (p1)-[:FRIENDS_WITH]->(p2)
RETURN p1, p2;

MATCH (p:Person)
WHERE p.age < 20
WITH p
LIMIT 100
DETACH DELETE p;

CALL apoc.periodic.iterate(
  "UNWIND range(1, 100000) AS id RETURN id",
  "CREATE (p:Person {id: id, name: 'Person ' + id, age: toInteger(rand() * 100)})",
  {batchSize: 1000, parallel: true}
);

CALL apoc.periodic.iterate(
  "MATCH (p1:Person), (p2:Person) WHERE p1 <> p2 RETURN p1, p2",
  "CREATE (p1)-[:KNOWS]->(p2)",
  {batchSize: 1000, parallel: true}
);

CALL apoc.periodic.iterate(
  "MATCH (p:Person) RETURN p",
  "WITH p
   SET p.visited = coalesce(p.visited, 0) + 1
   RETURN p",
  {batchSize: 100, parallel: true}
);
```

To run this script from the command line:

```sh
cypher-shell -u <username> -p <password> -f load-test.cypher
```


---------------

To connect to a remote Neo4j server using `cypher-shell`, you'll need to specify the server's address, port, and optionally provide authentication credentials. Here's how you can do it:

### Basic Connection Command

```sh
cypher-shell -a <host>:<port> -u <username> -p <password>
```

### Example

If your Neo4j server is hosted on `example.com` with the default port `7687` and your credentials are `neo4j` for both username and password, the command would be:

```sh
cypher-shell -a example.com:7687 -u neo4j -p neo4j
```

### Using a Connection URI

You can also use a connection URI that includes the scheme, host, port, and optionally the username and password:

```sh
cypher-shell bolt://<username>:<password>@<host>:<port>
```

### Example

Using the connection URI for the same server:

```sh
cypher-shell bolt://neo4j:neo4j@example.com:7687
```

### Using Environment Variables

For security reasons, you might not want to include your password directly in the command line. You can set the `NEO4J_USERNAME` and `NEO4J_PASSWORD` environment variables instead:

```sh
export NEO4J_USERNAME=neo4j
export NEO4J_PASSWORD=neo4j
cypher-shell -a example.com:7687
```

### SSL/TLS Connection

If your Neo4j server requires an SSL/TLS connection, you can add the `--encryption` option:

```sh
cypher-shell -a example.com:7687 -u neo4j -p neo4j --encryption true
```

### Full Example with All Options

Here’s a full example of connecting to a remote Neo4j server with all options:

```sh
cypher-shell -a example.com:7687 -u neo4j -p neo4j --encryption true
```

### Summary of Options

- `-a, --address` : The address of the remote Neo4j server (`<host>:<port>`).
- `-u, --username` : The username for authentication.
- `-p, --password` : The password for authentication.
- `--encryption` : Use `true` to enable SSL/TLS connection.

This should allow you to connect to your remote Neo4j server using `cypher-shell`.
