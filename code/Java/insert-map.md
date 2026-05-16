# Prompt
Create a Java Class InsertRepository.class that uses JDBC and DatabaseMetadata to identify type of database and receive two parameters:

1. String tableName - Name of table to insert data

2. Map<String, Object> - A Map with content to translate the keys to column names, converting to corret data type, based on DatabaseMetadata

The class must be have logic to identify type of database (MySQL, PostgreSQL, Oracle and SQL Server) and be smart to adopt the correct strategy to manipulate Primary Key, example: if database is Oracle, it must identify the correct sequence and set the next value on primary key atribute of Map.
The method insert must return a Map<String,Object> with primary key, if not sent in parameter.

# Enhanced prompt
Act as a Senior Java Developer specializing in JDBC and database abstraction. Create a Java class named `InsertRepository` that performs dynamic SQL insertions using `DatabaseMetaData`.

Requirements:
1. Constructor: Must accept a `javax.sql.DataSource` or `Connection` object.
2. Database Identification: Implement logic to detect the database product name (MySQL, PostgreSQL, Oracle, SQL Server) using `DatabaseMetaData.getDatabaseProductName()`.
3. Method Signature: `public Map<String, Object> insert(String tableName, Map<String, Object> data)`.
4. Logic Specifications:
   - Metadata Lookup: Use `DatabaseMetaData.getColumns()` to map input keys to valid database column names and perform type conversion (e.g., handling numeric formats, timestamps, or strings based on `java.sql.Types`).
   - Strategy Pattern for Primary Keys: Implement a strategy-based approach to handle auto-incrementing/identity columns. 
     - For Oracle: Query metadata to identify the associated `SEQUENCE` and fetch `sequence.nextval`.
     - For others (MySQL/PostgreSQL/SQL Server): Handle IDENTITY columns or standard auto-increment mechanisms.
   - SQL Construction: Build a secure `PreparedStatement` to prevent SQL injection.
   - Return Value: The method must return the full record map, specifically including the auto-generated primary key value if it was not provided in the input map.
5. Error Handling: Include robust exception handling for `SQLException` and data type mismatch scenarios.
6. Clean Code: Ensure the solution is modular, uses try-with-resources, and follows enterprise Java standards. 

Provide the complete source code, including imports and a brief explanation of how the strategy pattern for primary key retrieval is implemented for each database type.

