# PortSwigger Lab Writeup: SQL Injection - Querying Database Type and Version on MySQL and Microsoft

## Objective
The objective of this laboratory exercise is to exploit an SQL injection vulnerability to determine the database version information from a MySQL or Microsoft SQL Server (MSSQL) database.

* **Lab URL**: https://portswigger.net/web-security/sql-injection/examining-the-database/lab-querying-database-version-mysql-microsoft
* **Category**: SQL Injection
* **Difficulty**: Practitioner

## Payloads Utilized
* **Payload (Version Extraction - Successful):**
  `Gifts'+UNION+SELECT+@@version,NULL#`

## Exploitation Methodology

### Step 1: Application Observation
* The vulnerability exists within the category filter parameter.
* Unlike Oracle, MySQL and MSSQL do not strictly require a `FROM` clause for simple constant selections during column discovery.

### Step 2: Identification of the Vulnerable Endpoint
* The target parameter is `/filter?category=...`.

### Step 3: Payload Injection
* To retrieve the version on these systems, the `@@version` system variable is utilized. Furthermore, the comment syntax for MySQL frequently relies on the hash character (`#`) or `-- ` (dash-dash-space). URL-encoding the hash character as `%23` is often necessary depending on the browser.
* Inject the payload: `Gifts'+UNION+SELECT+@@version,NULL#` (encoded as `Gifts'+UNION+SELECT+@@version,NULL%23` if executed directly in the URL bar).
* The application processes the payload and outputs the database version string (e.g., Ubuntu MySQL version details) within the product listing, completing the laboratory exercise.

## Conclusion
* This laboratory reinforces the importance of database fingerprinting. 
* Attackers must adapt their payloads, including system variables (`@@version`) and comment syntax (`#`), to align with the specific backend technology.