## Neo4j Backup and Restore 

### Prerequisites

1. Ensure Neo4j is installed and running on your machine.
2. `neo4j-admin` tool is available in the Neo4j installation directory.
3. You have necessary permissions to perform backup and restore operations.

### Taking Offline Backups

Offline backups are taken when the database is not running.

#### Step 1: Stop the Neo4j Service

For Windows:
```sh
net stop neo4j
```

For Linux:
```sh
sudo systemctl stop neo4j
```

#### Step 2: Take the Backup

For Windows:
```sh
neo4j-admin.bat database dump neo4j --to-path=c:\backup
```

For Linux:
```sh
bin/neo4j-admin database dump neo4j --to-path=/path/to/backups/neo4j
```

#### Step 3: Start the Neo4j Service

For Windows:
```sh
net start neo4j
```

For Linux:
```sh
sudo systemctl start neo4j
```

### Taking Online Backups

Online backups can be taken while the database is running. This requires Neo4j Enterprise Edition.

#### Step 1: Take a Full Backup

For Windows:
```sh
neo4j-admin.bat database backup --type=full --to-path=c:\backup neo4j
```

For Linux:
```sh
bin/neo4j-admin database backup --type=full --to-path=/path/to/backups/neo4j neo4j
```

### Restoring Backups

#### Step 1: Restore the Backup

For Windows:
```sh
neo4j-admin.bat database restore --from-path=c:\backup\neo4j-2024-05-17T03-23-19.backup neo4jbackup
```

For Linux:
```sh
bin/neo4j-admin database restore --from-path=/path/to/backups/neo4j-2024-05-17T03-23-19.backup neo4jbackup
```

#### Step 2: Optional Point-in-Time Restore

You can restore the database to a specific point in time.

For Windows:
```sh
neo4j-admin.bat database restore --from-path=c:\backup\neo4j-2024-05-17T03-23-19.backup --restore-until="2024-05-17 03:25:25" neo4jbackup2
```

For Linux:
```sh
bin/neo4j-admin database restore --from-path=/path/to/backups/neo4j-2024-05-17T03-23-19.backup --restore-until="2024-05-17T03:25:25" neo4jbackup2
```

### Aggregate Backup

Combine multiple backups into a single aggregated backup.

For Windows:
```sh
neo4j-admin.bat database aggregate-backup --from-path=c:\backup neo4j
```

For Linux:
```sh
bin/neo4j-admin database aggregate-backup --from-path=/path/to/backups/neo4j neo4j
```

### Executing Cypher File After Restore

If you need to execute a Cypher file after restoring, you can use `cypher-shell`.

For Windows:
```sh
cypher-shell -u <username> -p <password> -f "C:\Users\Administrator\.Neo4jDesktop\relate-data\dbmss\dbms-7772a15a-ae0b-4176-9355-1b49cd3881a1\data\scripts\neo4jbackup2\restore_metadata.cypher"
```

For Linux:
```sh
cypher-shell -u <username> -p <password> -f "/path/to/restore_metadata.cypher"
```

Replace `<username>` and `<password>` with your Neo4j credentials.

### Summary

- **Offline Backups**: Stop the database, dump the data, and start the database.
- **Online Backups**: Use the `backup` command with the `--type=full` option.
- **Restore Backups**: Use the `restore` command with the `--from-path` option.
- **Aggregate Backups**: Combine multiple backups using `aggregate-backup`.
- **Execute Cypher After Restore**: Use `cypher-shell` to execute a Cypher file.
