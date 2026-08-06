# PortSwigger Lab Writeup: SQL Injection UNION Attack - Finding a Column Containing Text

## Objective
The objective of this laboratory exercise is to exploit an SQL injection vulnerability using a UNION attack to identify which of the returned columns can accommodate string data. The application filters products using user-supplied input insecurely. The goal is to inject a specific alphanumeric string provided by the lab instructions into the database response.

* **Lab URL**: https://portswigger.net/web-security/sql-injection/union-attacks/lab-find-column-containing-text
* **Category**: SQL Injection
* **Difficulty**: Practitioner

## Payloads Utilized
* **Payload 1 (Testing column 1 - Unsuccessful):**
  `Gifts'+UNION+SELECT+'[RANDOM_STRING]',NULL,NULL--`
* **Payload 2 (Testing column 2 - Successful):**
  `Gifts'+UNION+SELECT+NULL,'[RANDOM_STRING]',NULL--`

*(Note: Replace `[RANDOM_STRING]` with the specific string provided by the lab environment).*

## Exploitation Methodology

### Step 1: Application Observation
* Access the laboratory URL and observe the e-commerce platform. The vulnerability exists within the category filter.
* Previous enumeration (similar to Lab 3) indicates that the backend query returns three columns. 

### Step 2: Identification of the Vulnerable Endpoint
* The target endpoint is the `/filter?category=...` parameter.

### Step 3: Payload Injection
* To retrieve text data, at least one of the columns returned by the original query must be of a string data type. This is determined by replacing `NULL` values from the column discovery phase with a string payload.
* Inject the payload into the first column: `Gifts'+UNION+SELECT+'[RANDOM_STRING]',NULL,NULL--`. If this results in an Internal Server Error, the first column does not support text.
* Inject the payload into the second column: `Gifts'+UNION+SELECT+NULL,'[RANDOM_STRING]',NULL--`. 
* The application returns a successful response, and the injected string is rendered on the page, confirming the second column accommodates text data and successfully completing the laboratory exercise.

## Conclusion
* This laboratory demonstrates the secondary phase of a UNION-based SQL injection: determining data types.
* By systematically replacing `NULL` values with string literals, an attacker can identify which columns can be utilized to exfiltrate string-based information from the database.

## Related Common Weakness Enumerations (CWEs)

| CWE ID | Title | Description |
| :--- | :--- | :--- |
| CWE-89 | **Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection')** | Application fails to properly sanitize user input. |