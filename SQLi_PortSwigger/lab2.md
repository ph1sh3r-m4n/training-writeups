# PortSwigger Lab Writeup: SQL Injection Vulnerability Allowing Login Bypass

## Objective
The objective of this laboratory exercise is to exploit an SQL injection vulnerability within a web application's authentication mechanism. The application executes a database query utilizing user-supplied input in an insecure manner to verify credentials. The goal is to bypass the login function and gain unauthorized access as an administrator.

* **Lab URL**: [https://portswigger.net/web-security/sql-injection/lab-login-bypass](https://portswigger.net/web-security/sql-injection/lab-login-bypass)
* **Category**: SQL Injection
* **Difficulty**: Apprentice

## Payloads Utilized
* **Payload 1 (Username field - Successful):**
  `administrator'--`
* **Payload 2 (Password field - Successful):**
  `' or 1=1--`

## Exploitation Methodology

### Step 1: Application Observation
* Access the laboratory URL and observe the login page functionality.
* The application operates as an e-commerce platform featuring an authentication interface. The laboratory documentation indicates that the vulnerability resides within this login function, where user input is directly concatenated into an SQL query.

### Step 2: Payload Injection
* The login mechanism can be successfully bypassed utilizing two distinct methods: targeting the username field or targeting the password field.
* **Method 1:** Inject the payload `administrator'--` into the username field. This payload successfully authenticates the user as 'administrator' while neutralizing the remainder of the SQL query (specifically, the `AND password = '$password'` condition) via the comment indicator (`--`). Consequently, authentication is achieved regardless of the input provided in the password field.
* **Method 2:** Provide `administrator` in the username field and inject the payload `' or 1=1--` into the password field. The injected `OR 1=1` condition invariably evaluates to true, overriding the password validation logic and neutralizing subsequent conditions. This similarly bypasses the authentication requirement.
* Utilizing either method successfully circumvents the authentication controls, granting full access to the administrator account and completing the laboratory exercise.

## Conclusion
* This laboratory demonstrates a fundamental SQL injection vulnerability where user credentials submitted via a login form are directly inserted into a backend SQL query to perform authentication logic.
* Due to the absence of input sanitization and prepared statements, malicious actors can manipulate the query structure to execute arbitrary SQL commands. 
* This manipulation allows for the complete circumvention of the authentication mechanism, leading to unauthorized administrative access and the potential compromise of the underlying database.

## Related Common Weakness Enumerations (CWEs)

| CWE ID | Title | Description |
| :--- | :--- | :--- |
| CWE-89 | **Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection')** | The core vulnerability; input is not sanitized, allowing SQL injection into the authentication logic. |
| CWE-287 | **Improper Authentication** | The login mechanism is bypassed due to injection, indicating a fundamental flaw in the authentication logic. |
| CWE-20 | **Improper Input Validation** | The application fails to validate user input, enabling the successful injection of SQL meta-characters. |
| CWE-116 | **Improper Encoding or Escaping of Output** | The lack of appropriate escaping for SQL input contributes to the successful execution of the injection payload. |