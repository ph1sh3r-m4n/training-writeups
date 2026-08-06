# PortSwigger Lab Writeup: Reflected XSS into HTML Context with Nothing Encoded

## Objective
The objective of this laboratory exercise is to exploit a reflected Cross-Site Scripting (XSS) vulnerability. The application insecurely reflects user-supplied input directly into the HTML response without any encoding or sanitization. The goal is to execute an arbitrary JavaScript payload that triggers an alert function.

* **Lab URL**: https://portswigger.net/web-security/cross-site-scripting/reflected/lab-html-context-nothing-encoded
* **Category**: Cross-Site Scripting (XSS)
* **Difficulty**: Apprentice

## Payloads Utilized
* **Payload (JavaScript Execution - Successful):**
  `<script>alert(1)</script>`

## Exploitation Methodology

### Step 1: Application Observation
* Access the laboratory URL and interact with the application. The primary feature available is a search functionality.
* When submitting a search term (e.g., `test`), the application responds with a page displaying the phrase: "0 search results for 'test'".

### Step 2: Identification of the Vulnerable Endpoint
* The application reflects the input provided in the `search` parameter directly into the HTML response.
* Inspecting the page source reveals that the search term is placed within the `<h1>` HTML tags without any HTML entity encoding (e.g., `<` is not converted to `&lt;`).

### Step 3: Payload Injection
* Because the input is directly rendered into the HTML document, standard HTML tags can be injected to alter the page structure.
* Inject the payload `<script>alert(1)</script>` into the search box and submit the request.
* The application reflects the payload within the HTML context, rendering the `<script>` tags as executable HTML elements.
* The browser parses the injected script block and executes the JavaScript `alert(1)` function, successfully completing the laboratory exercise.

## Conclusion
* This laboratory demonstrates a fundamental reflected XSS vulnerability where untrusted user input is directly concatenated into the HTTP response.
* The absence of input sanitization and context-aware output encoding allows malicious actors to inject and execute arbitrary JavaScript within the context of the victim's session.

## Related Common Weakness Enumerations (CWEs)

| CWE ID | Title | Description |
| :--- | :--- | :--- |
| CWE-79 | **Improper Neutralization of Input During Web Page Generation ('Cross-site Scripting')** | The core vulnerability allowing arbitrary script execution. |
| CWE-116 | **Improper Encoding or Escaping of Output** | The application fails to safely encode characters like `<` and `>` before rendering. |