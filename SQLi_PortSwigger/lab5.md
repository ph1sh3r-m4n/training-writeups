# PortSwigger Lab Writeup: SQL Injection UNION Attack - Retrieving Data from Other Tables

## Objective
The objective of this laboratory exercise is to exploit an SQL injection vulnerability utilizing a UNION attack to retrieve data from a separate database table. The goal is to extract the credentials (username and password) of the 'administrator' user from the `users` table and successfully authenticate.

* **Lab URL**: https://portswigger.net/web-security/sql-injection/union-attacks/lab-retrieve-data-from-other-tables
* **Category**: SQL Injection
* **Difficulty**: Practitioner

## Payloads Utilized
* **Payload (Data Extraction - Successful):**
  `Gifts'+UNION+SELECT+username,password+FROM+users--`

## Exploitation Methodology

### Step 1: Application Observation
* The application is vulnerable to SQL injection within the product category filter.
* Prior enumeration confirms the original query returns two columns, both of which accept string data types.

### Step 2: Identification of the Vulnerable Endpoint
* The target parameter is `/filter?category=...`.

### Step 3: Payload Injection
* The laboratory documentation provides the target table name (`users`) and the target column names (`username` and `password`).
* Inject the payload: `Gifts'+UNION+SELECT+username,password+FROM+users--` into the category parameter.
* This payload neutralizes the original query's constraints and appends a new query that selects the `username` and `password` columns from the `users` table.
* The application's response renders the contents of the `users` table within the product listing area, disclosing the administrator's password.
* Utilize the extracted credentials to log in as the administrator, completing the laboratory exercise.

## Conclusion
* This laboratory illustrates the primary objective of a UNION attack: unauthorized data exfiltration.
* Having mapped the column count and data types, an attacker can construct queries to extract sensitive information from any accessible table within the database.

## Related Common Weakness Enumerations (CWEs)

| CWE ID | Title | Description |
| :--- | :--- | :--- |
| CWE-89 | **Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection')** | The core vulnerability allowing arbitrary query execution. |
| CWE-200 | **Exposure of Sensitive Information to an Unauthorized Actor** | The result of the vulnerability, exposing user credentials. |