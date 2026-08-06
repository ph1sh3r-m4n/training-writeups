# PortSwigger Lab Writeup: SQL Injection UNION Attack - Retrieving Multiple Values in a Single Column

## Objective
The objective of this laboratory exercise is to exploit an SQL injection vulnerability using a UNION attack to retrieve data from another table when only a single column in the original query supports text data. The goal is to extract the administrator's credentials and authenticate.

* **Lab URL**: https://portswigger.net/web-security/sql-injection/union-attacks/lab-retrieve-multiple-values-in-single-column
* **Category**: SQL Injection
* **Difficulty**: Practitioner

## Payloads Utilized
* **Payload (String Concatenation - Successful):**
  `Gifts'+UNION+SELECT+NULL,username||'~'||password+FROM+users--`

## Exploitation Methodology

### Step 1: Application Observation
* The application features a vulnerable category filter.
* Enumeration reveals the query returns two columns, but only one column accommodates string data types.

### Step 2: Identification of the Vulnerable Endpoint
* The target parameter is `/filter?category=...`.

### Step 3: Payload Injection
* Because only one string column is available, multiple pieces of data (username and password) must be concatenated into a single string for exfiltration.
* The application utilizes a PostgreSQL database (inferred from the `||` concatenation operator).
* Inject the payload: `Gifts'+UNION+SELECT+NULL,username||'~'||password+FROM+users--`.
* This payload concatenates the `username`, a separator (`~`), and the `password` into the single available text column.
* The response displays the concatenated credentials (e.g., `administrator~[password]`).
* Extract the password, navigate to the login portal, and authenticate as the administrator to complete the laboratory.

## Conclusion
* This laboratory demonstrates advanced data exfiltration techniques when limited by column data types.
* String concatenation functions (which vary by database vendor) allow attackers to bypass column restrictions and retrieve multiple data points simultaneously.