In Neo4j, you can retrieve the current configuration parameters using Cypher queries combined with the `dbms.listConfig` procedure. To change these parameters, you typically need to modify the `neo4j.conf` file or use the `dbms.setConfigValue` procedure. The duration of these changes depends on how they are applied.

Here's how you can list the current configuration parameters:

### Listing Configuration Parameters

To see the current configuration parameters, you can use the following Cypher query:

```cypher
CALL dbms.listConfig()
YIELD name, value
RETURN name, value
```

This query will return a list of configuration parameters along with their current values.

### Changing Configuration Parameters

To change configuration parameters, you can use the `dbms.setConfigValue` procedure. For example:

```cypher
CALL dbms.setConfigValue('dbms.memory.heap.initial_size', '512m')
```

This will set the initial heap size to 512 MB. Note that this change is temporary and will only last until the Neo4j server is restarted. For permanent changes, you need to update the `neo4j.conf` file.

### Duration of Configuration Changes

1. **Temporary Changes:** Changes made using `dbms.setConfigValue` are temporary and will revert to their original values upon restarting the Neo4j server.

2. **Permanent Changes:** To make permanent changes, you should update the `neo4j.conf` file. Here’s how you can do it:
   - Locate the `neo4j.conf` file in your Neo4j installation directory (typically found in `conf/neo4j.conf`).
   - Open the file in a text editor.
   - Add or modify the desired configuration parameters.
   - Save the file and restart the Neo4j server for the changes to take effect.

### Example of Permanent Change

To permanently change the initial heap size, you would add or modify the following line in the `neo4j.conf` file:

```plaintext
dbms.memory.heap.initial_size=512m
```

After saving the file, restart the Neo4j server:

```sh
sudo systemctl restart neo4j
```

or

```sh
neo4j restart
```

--------------------------------------------

