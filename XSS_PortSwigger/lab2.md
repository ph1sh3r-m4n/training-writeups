# PortSwigger Lab Writeup: Stored XSS into HTML Context with Nothing Encoded

## Objective
The objective of this laboratory exercise is to exploit a stored Cross-Site Scripting (XSS) vulnerability. The application stores user-supplied input in a database and subsequently renders it on a web page without proper encoding. The goal is to inject a persistent JavaScript payload that executes an alert function whenever a user visits the affected page.

* **Lab URL**: https://portswigger.net/web-security/cross-site-scripting/stored/lab-html-context-nothing-encoded
* **Category**: Cross-Site Scripting (XSS)
* **Difficulty**: Apprentice

## Payloads Utilized
* **Payload (JavaScript Execution - Successful):**
  `<script>alert(1)</script>`

## Exploitation Methodology

### Step 1: Application Observation
* Access the laboratory URL, which hosts a blog platform.
* Navigate to any individual blog post. The page contains a comment section where users can submit a name, email, website, and a comment body.

### Step 2: Identification of the Vulnerable Endpoint
* Submit a benign test comment to observe how the application handles the input.
* Upon returning to the blog post, the submitted comment is displayed. Inspecting the page source reveals that the comment body is rendered directly into the HTML structure without encoding.

### Step 3: Payload Injection
* Submit a new comment, but this time insert the malicious payload `<script>alert(1)</script>` into the "Comment" field.
* Fill out the remaining required fields (Name, Email) with arbitrary data and submit the form.
* Navigate back to the blog post. When the page loads, the server retrieves the stored comment containing the payload and renders it directly into the HTML document.
* The browser interprets the injected `<script>` tags and executes the JavaScript `alert(1)` function, successfully completing the laboratory.

## Conclusion
* This laboratory demonstrates a classic stored XSS vulnerability, which is generally more severe than reflected XSS because the payload is persistently stored on the server.
* Any user navigating to the compromised page will inadvertently execute the malicious script, allowing attackers to hijack sessions, perform unauthorized actions, or distribute malware.

## Related Common Weakness Enumerations (CWEs)

| CWE ID | Title | Description |
| :--- | :--- | :--- |
| CWE-79 | **Improper Neutralization of Input During Web Page Generation ('Cross-site Scripting')** | The vulnerability allowing script injection. |
| CWE-116 | **Improper Encoding or Escaping of Output** | The application fails to encode data retrieved from the database before rendering. |