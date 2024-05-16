### Steps to Disable Authentication, Reset Password, and Re-enable Authentication

1. **Stop the Neo4j Service**

   Before making any changes, stop the Neo4j service:

   ```bash
   sudo systemctl stop neo4j
   ```

2. **Disable Authentication**

   Edit the Neo4j configuration file, typically located at `/etc/neo4j/neo4j.conf`. You need to set the `dbms.security.auth_enabled` property to `false`.

   Open the configuration file in a text editor:

   ```bash
   sudo nano /etc/neo4j/neo4j.conf
   ```

   Find the following line:

   ```plaintext
   #dbms.security.auth_enabled=true
   ```

   Uncomment it and change it to:

   ```plaintext
   dbms.security.auth_enabled=false
   ```

   Save the file and exit the text editor.

3. **Start the Neo4j Service**

   Restart the Neo4j service with authentication disabled:

   ```bash
   sudo systemctl start neo4j
   ```

4. **Access Neo4j Without Authentication**

   Open Neo4j Browser (typically at `http://localhost:7474`), and you should be able to access it without needing to log in.

5. **Reset the Admin Password**

   Use the following Cypher command to reset the admin password:

   ```cypher
   ALTER USER neo4j SET PASSWORD 'new_admin_password';
   ```

6. **Re-enable Authentication**

   After resetting the password, stop the Neo4j service again:

   ```bash
   sudo systemctl stop neo4j
   ```

   Edit the `neo4j.conf` file again to re-enable authentication:

   ```bash
   sudo nano /etc/neo4j/neo4j.conf
   ```

   Change the `dbms.security.auth_enabled` property back to `true`:

   ```plaintext
   dbms.security.auth_enabled=true
   ```

   Save the file and exit the text editor.

7. **Start the Neo4j Service**

   Start the Neo4j service with authentication re-enabled:

   ```bash
   sudo systemctl start neo4j
   ```

8. **Log in with the New Admin Password**

   Open Neo4j Browser and log in using the `neo4j` user and the new password you set.
