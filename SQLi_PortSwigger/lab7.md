# PortSwigger Lab Writeup: SQL Injection - Querying Database Type and Version on Oracle

## Objective
The objective of this laboratory exercise is to exploit an SQL injection vulnerability to extract the database version information from an Oracle database. 

* **Lab URL**: https://portswigger.net/web-security/sql-injection/examining-the-database/lab-querying-database-version-oracle
* **Category**: SQL Injection
* **Difficulty**: Practitioner

## Payloads Utilized
* **Payload (Version Extraction - Successful):**
  `Gifts'+UNION+SELECT+BANNER,NULL+FROM+v$version--`

## Exploitation Methodology

### Step 1: Application Observation
* The application is vulnerable within the category filter.
* For Oracle databases, every `SELECT` statement must include a `FROM` clause. During initial column discovery, a dummy table like `DUAL` must be used (e.g., `UNION SELECT NULL,NULL FROM DUAL--`).

### Step 2: Identification of the Vulnerable Endpoint
* The target parameter is `/filter?category=...`.

### Step 3: Payload Injection
* After determining that two columns are returned, the objective is to query the specific table containing Oracle version data: `v$version`.
* Inject the payload: `Gifts'+UNION+SELECT+BANNER,NULL+FROM+v$version--`.
* This query extracts the `BANNER` column from the `v$version` table, which houses the detailed version string.
* The application renders the Oracle version information on the page, satisfying the laboratory requirements.

## Conclusion
* This laboratory highlights the nuances of SQL injection across different database management systems. 
* Recognizing Oracle-specific syntax (such as the requirement for the `DUAL` table) and system tables (`v$version`) is critical for successful fingerprinting and exploitation.