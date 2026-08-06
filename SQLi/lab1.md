# 🗂️ PortSwigger Lab Writeup: SQL Injection Vulnerability in WHERE clause Allowing Retrieval of Hidden Data

## 🎯 Objective
The objective of this lab is to exploit a **SQL injection vulnerability** in a web application where the application executes a SQL query with user-supplied input in an unsafe way to filter the products as well as show only released products, and our goal is to view all the hidden (unreleased) products through SQL injection.

* **Lab URL**: [https://portswigger.net/web-security/sql-injection/lab-retrieve-hidden-data](https://portswigger.net/web-security/sql-injection/lab-retrieve-hidden-data)
* **Category**: SQL Injection
* **Difficulty**: Apprentice

## 💉 Payloads Used
* **Payload 1 (showed only hidden Gifts products)** - ❌
  `Gifts'--`
* **Payload 2 (showed all hidden products)** - ✅
  `Gifts' or 1=1--`

## 🧪 Exploitation Steps

### 🕵️ Step 1: Observe the Website
* Firstly open the lab URL in your browser, and observe what it is about and how it works.
* At first glance, the website seems to be a shopping website with an option to filter products on different categories. In the lab description, it is mentioned that the vulnerability is in the filter parameter which is being used directly in a SQL query.

### 🔍 Step 2: Find the Vulnerable Endpoint
* Click on any one of the filters to see how the website behaves and where the filter parameter is passed to the server.
* Here, the site filters the products by supplying the category name in the URL - `/filter?category=...`

### 🚀 Step 3: Inject the Payload
* Based on the provided SQL query in the lab description, we can inject the `'--` along with the category name which will comment out the remaining SQL query - `AND released = 1`
* Hence, we try our payload - `Gifts'--` which when executed will result in a query - `SELECT * FROM products WHERE category = 'Gifts'--` allowing us to view both released and unreleased products.
* Hence, the payload worked and showed us the hidden product but still the lab was not solved because our task was to view all the hidden products of all types of categories instead of a specific one.
* Therefore, we try another payload - `Gifts' or 1=1--` which when executed will result in a query - `SELECT * FROM products WHERE category = 'Gifts' or 1=1--` allowing us to view all hidden products.
* The above query will always result in True for the `WHERE` clause, resulting in showing all the products without any condition.
* And 💥 Booom!, We **got to see all the hidden products**.
* And Finally, **the Lab is solved**.

## 🧠 Conclusion
* This lab involves **a very basic case of SQL injection vulnerability**, where the category parameter is used to filter products **where the application directly concatenates the user-supplied category in the SQL query to fetch only the relevant products and also currently released to the public**.
* Since the application neither validates the user-input nor uses prepared statements, **an attacker can manipulate the SQL query by injecting any arbitrary SQL commands to gain full access to the database.**
* By injecting `' or 1=1--` in the category parameter, we are able to manipulate the SQL query and view the hidden products.

## 🧾 Related CWEs

| CWE ID | Title | Description |
| :--- | :--- | :--- |
| CWE-89 | **Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection')** | Application fails to properly sanitize user input before using it in SQL queries. |
| CWE-20 | **Improper Input Validation** | The app does not validate user input properly, allowing malicious input to influence behavior. |
| CWE-713 | **Owning Dangerous Functionality Without Adequate Security Controls** | SQL capabilities are exposed without secure controls. |
| CWE-116 | **Improper Encoding or Escaping of Output** | Failure to safely encode input before inserting it into SQL queries. |
