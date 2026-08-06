# PortSwigger Lab Writeup: SQL Injection - Listing Database Contents on Non-Oracle Databases

## Objective
The objective of this laboratory exercise is to exploit an SQL injection vulnerability to map the database structure. The goal is to query the `information_schema` to identify the table holding user credentials, identify the relevant columns, and ultimately extract the administrator's password to authenticate.

* **Lab URL**: https://portswigger.net/web-security/sql-injection/examining-the-database/lab-listing-database-contents-non-oracle
* **Category**: SQL Injection
* **Difficulty**: Practitioner

## Payloads Utilized
* **Payload 1 (Table Enumeration):**
  `Gifts'+UNION+SELECT+table_name,NULL+FROM+information_schema.tables--`
* **Payload 2 (Column Enumeration):**
  `Gifts'+UNION+SELECT+column_name,NULL+FROM+information_schema.columns+WHERE+table_name='users_abcdef'--`
* **Payload 3 (Data Extraction):**
  `Gifts'+UNION+SELECT+username_abcdef,password_abcdef+FROM+users_abcdef--`

*(Note: 'abcdef' represents the randomized string appended to table and column names in the lab environment).*

## Exploitation Methodology

### Step 1: Application Observation
* The application contains a vulnerable category filter and utilizes a non-Oracle database (which implements the standard `information_schema`).

### Step 2: Payload Injection - Table Enumeration
* Inject Payload 1 to list all tables. Review the response to identify a table name resembling `users` (e.g., `users_gwkxqy`).

### Step 3: Payload Injection - Column Enumeration
* Utilize the identified table name to query the columns. Inject Payload 2: `Gifts'+UNION+SELECT+column_name,NULL+FROM+information_schema.columns+WHERE+table_name='users_gwkxqy'--`.
* Review the output to identify columns corresponding to usernames and passwords (e.g., `username_tzjhvb`, `password_pqowie`).

### Step 4: Payload Injection - Data Extraction
* Construct the final query using the discovered table and column names. Inject Payload 3: `Gifts'+UNION+SELECT+username_tzjhvb,password_pqowie+FROM+users_gwkxqy--`.
* Extract the administrator password from the resulting output and authenticate via the login portal to complete the exercise.

## Conclusion
* This laboratory demonstrates a complete database enumeration attack chain. 
* Standardized metadata repositories like `information_schema` allow attackers to dynamically discover database schemas, rendering defense-by-obscurity (like randomized table names) ineffective against SQL injection.