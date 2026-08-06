# PortSwigger Lab Writeup: SQL Injection UNION Attack - Determining the Number of Columns Returned by the Query

## Objective
The objective of this laboratory exercise is to exploit an SQL injection vulnerability by executing a UNION attack. The application utilizes user-supplied input within a product category filter insecurely. The goal is to determine the exact number of columns returned by the initial database query by appending a UNION SELECT clause containing NULL values.

* **Lab URL**: https://portswigger.net/web-security/sql-injection/union-attacks/lab-determine-number-of-columns
* **Category**: SQL Injection
* **Difficulty**: Practitioner

## Payloads Utilized
* **Payload 1 (Incorrect column count - Unsuccessful):**
  `Gifts'+UNION+SELECT+NULL--`
* **Payload 2 (Incorrect column count - Unsuccessful):**
  `Gifts'+UNION+SELECT+NULL,NULL--`
* **Payload 3 (Correct column count - Successful):**
  `Gifts'+UNION+SELECT+NULL,NULL,NULL--`

## Exploitation Methodology

### Step 1: Application Observation
* Access the laboratory URL and observe the e-commerce platform's functionality.
* The application filters products based on categories. The vulnerability is documented to exist within the category filter parameter, which is directly concatenated into a backend SQL query.

### Step 2: Identification of the Vulnerable Endpoint
* Interact with the category filters to observe the server's handling of the parameter.
* The application filters products by appending the category name to the URL, specifically within the `/filter?category=...` parameter.

### Step 3: Payload Injection
* To execute a successful UNION attack, the injected query must return the same number of columns as the original query. This is achieved by iteratively injecting `NULL` values until the server returns a valid response instead of an error.
* Injecting the initial payload, `Gifts'+UNION+SELECT+NULL--`, results in an Internal Server Error, indicating a column count mismatch.
* Injecting the second payload, `Gifts'+UNION+SELECT+NULL,NULL--`, also yields an error.
* Injecting the third payload, `Gifts'+UNION+SELECT+NULL,NULL,NULL--`, returns a standard HTTP 200 response displaying the products. 
* The successful execution of this payload confirms that the original query returns exactly three columns, completing the laboratory exercise.

## Conclusion
* This laboratory demonstrates the foundational step for UNION-based SQL injection attacks: determining the column count.
* Due to the lack of input sanitization, an attacker can manipulate the query using the `UNION` operator.
* By systematically injecting `NULL` values, the structure of the underlying database query is mapped, enabling subsequent data exfiltration.

## Related Common Weakness Enumerations (CWEs)

| CWE ID | Title | Description |
| :--- | :--- | :--- |
| CWE-89 | **Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection')** | Application fails to properly sanitize user input before using it in SQL queries. |
| CWE-20 | **Improper Input Validation** | The application does not validate user input properly, allowing malicious input to influence behavior. |
| CWE-116 | **Improper Encoding or Escaping of Output** | Failure to safely encode input before inserting it into SQL queries. |