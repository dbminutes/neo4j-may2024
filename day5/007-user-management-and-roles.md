
#### 1. **Creating and Managing Users**

**Creating a User:**
To create a new user in Neo4j, use the `CREATE USER` command:

```cypher
CREATE USER username SET PASSWORD 'password' CHANGE NOT REQUIRED
```

- `username`: The name of the new user.
- `password`: The password for the new user.
- `CHANGE NOT REQUIRED`: Specifies that the user is not required to change the password on the first login.

**Example:**
```cypher
CREATE USER alice SET PASSWORD 'securePassword123' CHANGE NOT REQUIRED
```

**Listing Users:**
To list all users in the database:

```cypher
SHOW USERS
```

**Deleting a User:**
To delete a user:

```cypher
DROP USER username
```

**Example:**
```cypher
DROP USER alice
```

#### 2. **Roles in Neo4j**

Neo4j uses roles to manage permissions for users. Some of the key roles are:

- **admin**: Full access to all database operations, including user and role management.
- **architect**: Can manage databases, indexes, and constraints but cannot manage users and roles.
- **publisher**: Can write and manage data but cannot manage users, roles, or schema.
- **reader**: Can read data but cannot write or manage schema.
- **editor**: Can read and write data but cannot manage schema or users and roles.

**Creating a Role:**
To create a new role:

```cypher
CREATE ROLE role_name
```

**Example:**
```cypher
CREATE ROLE analyst
```

**Assigning Roles to Users:**
To assign a role to a user:

```cypher
GRANT ROLE role_name TO username
```

**Example:**
```cypher
GRANT ROLE reader TO alice
```

**Revoking Roles from Users:**
To revoke a role from a user:

```cypher
REVOKE ROLE role_name FROM username
```

**Example:**
```cypher
REVOKE ROLE reader FROM alice
```

**Listing Roles:**
To list all roles in the database:

```cypher
SHOW ROLES
```

**Dropping a Role:**
To drop a role:

```cypher
DROP ROLE role_name
```

**Example:**
```cypher
DROP ROLE analyst
```

#### 3. **User Management Activities**

**Resetting a User's Password:**
To reset a user's password if they forgot it:

```cypher
ALTER USER username SET PASSWORD 'new_password'
```

**Example:**
```cypher
ALTER USER alice SET PASSWORD 'newSecurePassword123'
```

**Requiring Password Change on Next Login:**
To force a user to change their password on the next login:

```cypher
ALTER USER username SET PASSWORD 'new_password' CHANGE REQUIRED
```

**Example:**
```cypher
ALTER USER alice SET PASSWORD 'temporaryPassword123' CHANGE REQUIRED
```

**Disabling a User:**
To disable a user account:

```cypher
ALTER USER username SET STATUS SUSPENDED
```

**Example:**
```cypher
ALTER USER alice SET STATUS SUSPENDED
```

**Enabling a User:**
To re-enable a user account:

```cypher
ALTER USER username SET STATUS ACTIVE
```

**Example:**
```cypher
ALTER USER alice SET STATUS ACTIVE
```

#### 4. **Checking Role Privileges**

To check the privileges assigned to a specific role, use the `SHOW ROLE` command:

```cypher
SHOW ROLE role_name PRIVILEGES
```

**Example:**
```cypher
SHOW ROLE reader PRIVILEGES
```

This command lists all the privileges that have been granted to the specified role.

#### 5. **Checking User Roles**

To see which roles have been assigned to a specific user, filter the output of `SHOW ROLES WITH USERS`:

```cypher
SHOW ROLES WITH USERS WHERE member = 'alice'
```

**Example Output:**
```
Role   | Member
------------------
reader | alice
```

#### 6. **Example Workflow: Alice's Roles and Privileges**

**Step 1: Find Alice's Roles**
```cypher
SHOW ROLES WITH USERS WHERE member = 'alice'
```

**Example Output:**
```
Role   | Member
------------------
reader | alice
```

**Step 2: Show Privileges for Each Role Assigned to Alice**
Since Alice has the `reader` role:

```cypher
SHOW ROLE reader PRIVILEGES
```

**Example Output:**
```
Role    | Resource   | Action
-----------------------------------------
reader  | DATABASE   | ACCESS
reader  | GRAPH      | READ
reader  | SCHEMA     | READ
```

### Summary

To manage users and roles in Neo4j:
1. **Create and manage users** with `CREATE USER`, `SHOW USERS`, and `DROP USER`.
2. **Create and manage roles** with `CREATE ROLE`, `GRANT ROLE`, `REVOKE ROLE`, and `SHOW ROLES`.
3. **Reset passwords and manage user statuses** with `ALTER USER`.
4. **Check role privileges** using `SHOW ROLE role_name PRIVILEGES`.
5. **Check user roles** using `SHOW ROLES WITH USERS WHERE member = 'username'`.


-------------------------------

#### 1. **Creating a New Database**

To create a new database, you need to be connected as a user with the `admin` role. Here is the command to create a new database named `exampleDB`:

```cypher
CREATE DATABASE exampleDB
```

You can check the status of the database to ensure it has been created successfully:

```cypher
SHOW DATABASE exampleDB
```

#### 2. **Creating a New Role with Full Access to the Database**

Let's create a new role named `dbManager` that will have full access to `exampleDB`.

**Step 1: Create the Role**
```cypher
CREATE ROLE dbManager
```

**Step 2: Grant Full Access to the Database**
```cypher
GRANT ACCESS ON DATABASE exampleDB TO dbManager
GRANT ALL PRIVILEGES ON DATABASE exampleDB TO dbManager
```

#### 3. **Creating a New Role with Read-Only Access to the Database**

Next, create a role named `dbReader` that will have read-only access to `exampleDB`.

**Step 1: Create the Role**
```cypher
CREATE ROLE dbReader
```

**Step 2: Grant Read-Only Access to the Database**
```cypher
GRANT ACCESS ON DATABASE exampleDB TO dbReader
GRANT MATCH {*} ON GRAPH exampleDB NODES * TO dbReader
GRANT MATCH {*} ON GRAPH exampleDB RELATIONSHIPS * TO dbReader
```

#### 4. **Assigning Roles to Users**

Now, assign these roles to users as needed.

**Example: Assigning `dbManager` Role to a User**
```cypher
GRANT ROLE dbManager TO alice
```

**Example: Assigning `dbReader` Role to a User**
```cypher
GRANT ROLE dbReader TO bob
```

#### Summary of Commands

1. **Create the Database:**
    ```cypher
    CREATE DATABASE exampleDB
    ```

2. **Create and Configure `dbManager` Role:**
    ```cypher
    CREATE ROLE dbManager
    GRANT ACCESS ON DATABASE exampleDB TO dbManager
    GRANT ALL PRIVILEGES ON DATABASE exampleDB TO dbManager
    ```

3. **Create and Configure `dbReader` Role:**
    ```cypher
    CREATE ROLE dbReader
    GRANT ACCESS ON DATABASE exampleDB TO dbReader
    GRANT MATCH {*} ON GRAPH exampleDB NODES * TO dbReader
    GRANT MATCH {*} ON GRAPH exampleDB RELATIONSHIPS * TO dbReader
    ```

4. **Assign Roles to Users:**
    ```cypher
    GRANT ROLE dbManager TO alice
    GRANT ROLE dbReader TO bob
    ```

### Checking Privileges

To verify the privileges of the roles:

**For `dbManager`:**
```cypher
SHOW ROLE dbManager PRIVILEGES
```

**For `dbReader`:**
```cypher
SHOW ROLE dbReader PRIVILEGES
```

----------------------------
 **Creating Roles with Specific Node Label Access**

Let's create two roles:
- `productManager`: Has access to nodes with the label `Product`.
- `orderViewer`: Has read-only access to nodes with the label `Order`.

##### Creating the `productManager` Role

**Step 1: Create the Role**

```cypher
CREATE ROLE productManager;
```

**Step 2: Grant Access to Nodes with the Label `Product`**

```cypher
GRANT ACCESS ON DATABASE exampleDB TO productManager;
GRANT MATCH {*} ON GRAPH exampleDB NODES Product TO productManager;
GRANT CREATE ON GRAPH exampleDB NODES Product TO productManager;
GRANT SET PROPERTY {*} ON GRAPH exampleDB NODES Product TO productManager;
GRANT DELETE ON GRAPH exampleDB NODES Product TO productManager;
```

##### Creating the `orderViewer` Role

**Step 1: Create the Role**

```cypher
CREATE ROLE orderViewer;
```

**Step 2: Grant Read-Only Access to Nodes with the Label `Order`**

```cypher
GRANT ACCESS ON DATABASE exampleDB TO orderViewer;
GRANT MATCH {*} ON GRAPH exampleDB NODES Order TO orderViewer;
```

#### 3. **Assigning Roles to Users**

**Example: Assigning `productManager` Role to a User**

```cypher
GRANT ROLE productManager TO alice;
```

**Example: Assigning `orderViewer` Role to a User**

```cypher
GRANT ROLE orderViewer TO bob;
```

#### Summary of Commands

1. **Create the Database:**

```cypher
CREATE DATABASE exampleDB;
```

2. **Create and Configure `productManager` Role:**

```cypher
CREATE ROLE productManager;
GRANT ACCESS ON DATABASE exampleDB TO productManager;
GRANT MATCH {*} ON GRAPH exampleDB NODES Product TO productManager;
GRANT CREATE ON GRAPH exampleDB NODES Product TO productManager;
GRANT SET PROPERTY {*} ON GRAPH exampleDB NODES Product TO productManager;
GRANT DELETE ON GRAPH exampleDB NODES Product TO productManager;
```

3. **Create and Configure `orderViewer` Role:**

```cypher
CREATE ROLE orderViewer;
GRANT ACCESS ON DATABASE exampleDB TO orderViewer;
GRANT MATCH {*} ON GRAPH exampleDB NODES Order TO orderViewer;
```

4. **Assign Roles to Users:**

```cypher
GRANT ROLE productManager TO alice;
GRANT ROLE orderViewer TO bob;
```

### Verifying Role Privileges

To verify the privileges of the roles:

**For `productManager`:**

```cypher
SHOW ROLE productManager PRIVILEGES;
```

**For `orderViewer`:**

```cypher
SHOW ROLE orderViewer PRIVILEGES;
```
