# PortSwigger Lab Writeup: SQL Injection Vulnerability in WHERE Clause Allowing Retrieval of Hidden Data

## Objective
The objective of this laboratory exercise is to exploit an SQL injection vulnerability within a web application. The application executes a database query utilizing user-supplied input in an insecure manner to filter products and display only those that have been released. The goal is to retrieve all unreleased (hidden) products by executing an SQL injection attack.

* **Lab URL**: [https://portswigger.net/web-security/sql-injection/lab-retrieve-hidden-data](https://portswigger.net/web-security/sql-injection/lab-retrieve-hidden-data)
* **Category**: SQL Injection
* **Difficulty**: Apprentice

## Payloads Utilized
* **Payload 1 (Displays only hidden 'Gifts' products - Unsuccessful):**
  `Gifts'--`
* **Payload 2 (Displays all hidden products - Successful):**
  `Gifts' or 1=1--`

## Exploitation Methodology

### Step 1: Application Observation
* Access the laboratory URL to observe the application's functionality.
* The application operates as an e-commerce platform with a product filtering mechanism based on categories. The laboratory documentation indicates that the vulnerability resides within this filter parameter, which is directly concatenated into an SQL query.

### Step 2: Identification of the Vulnerable Endpoint
* Interact with the filtering mechanism to observe the server's handling of the parameter.
* The application filters products by appending the category name to the URL, specifically within the `/filter?category=...` parameter.

### Step 3: Payload Injection
* Based on the SQL query structure provided in the laboratory description, it is possible to inject a comment sequence (`'--`) trailing the category name. This effectively neutralizes the remainder of the SQL query (specifically, the `AND released = 1` condition).
* Injecting the initial payload, `Gifts'--`, results in the execution of the query: `SELECT * FROM products WHERE category = 'Gifts'--`. This permits the retrieval of both released and unreleased products within the 'Gifts' category.
* While this payload exposes hidden products, it does not fulfill the laboratory requirements, which necessitate the retrieval of all hidden products across all categories.
* Consequently, a secondary payload is required: `Gifts' or 1=1--`. Execution of this payload results in the query: `SELECT * FROM products WHERE category = 'Gifts' or 1=1--`.
* The injected `OR 1=1` condition always evaluates to true, overriding the initial category constraint and neutralizing subsequent conditions via the comment indicator (`--`). This causes the database to return all product records without restriction.
* Upon execution of this payload, all hidden products are successfully retrieved, completing the laboratory exercise.

## Conclusion
* This laboratory demonstrates a fundamental SQL injection vulnerability where user-supplied input via a category parameter is directly concatenated into a backend SQL query without sanitization or parameterization.
* Due to the absence of input validation and prepared statements, malicious actors can manipulate the query structure to execute arbitrary SQL commands and achieve unauthorized database access.
* By injecting the payload `' or 1=1--` into the category parameter, the query logic is successfully manipulated to disclose unauthorized information.

## Related Common Weakness Enumerations (CWEs)

| CWE ID | Title | Description |
| :--- | :--- | :--- |
| CWE-89 | **Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection')** | Application fails to properly sanitize user input before using it in SQL queries. |
| CWE-20 | **Improper Input Validation** | The application does not validate user input properly, allowing malicious input to influence behavior. |
| CWE-713 | **Owning Dangerous Functionality Without Adequate Security Controls** | SQL capabilities are exposed without secure controls. |
| CWE-116 | **Improper Encoding or Escaping of Output** | Failure to safely encode input before inserting it into SQL queries. |
