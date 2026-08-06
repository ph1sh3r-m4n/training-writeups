# PortSwigger Lab Writeup: Reflected DOM XSS

## Objective
The objective of this laboratory exercise is to exploit a reflected DOM XSS vulnerability. The application features a server that reflects user input within a JSON response. A client-side script evaluates this JSON response insecurely. The goal is to manipulate the JSON structure to execute arbitrary JavaScript.

* **Lab URL**: https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-dom-xss-reflected
* **Category**: Cross-Site Scripting (DOM XSS)
* **Difficulty**: Practitioner

## Payloads Utilized
* **Payload (JSON Breakout and JavaScript Execution - Successful):**
  `\"-alert(1)}//`

## Exploitation Methodology

### Step 1: Application Observation
* Access the laboratory URL and utilize the search functionality.
* The application conducts an asynchronous request (AJAX/Fetch) to a backend endpoint to retrieve search results.

### Step 2: Identification of the Vulnerable Endpoint
* Utilize browser developer tools (Network tab) to inspect the search request. 
* The server responds with a JSON object containing the reflected search term, for example: `{"searchTerm":"test", "results":[]}`.
* Inspect the client-side JavaScript handling this response (e.g., `searchResults.js`).
* The script retrieves the JSON response as a string and insecurely evaluates it using the `eval()` function to parse it into a JavaScript object: `var searchResults = eval('(' + this.responseText + ')');`.

### Step 3: Payload Injection
* To execute arbitrary code, the payload must break out of the JSON string context and append executable JavaScript before the `eval()` function completes evaluation.
* Inject the payload `\"-alert(1)}//` into the search field.
* The server reflects the payload within the JSON string. The server escapes the double quote, resulting in the following string being passed to `eval()`:
  `{"searchTerm":"\\"-alert(1)}//", "results":[]}`
* During execution, `eval()` processes the string. The escaped `\\"` successfully breaks out of the `searchTerm` string value. The `-` operator is used to subtract the result of the `alert(1)` function execution. The `}` prematurely closes the JSON object, and `//` comments out the remainder of the legitimate JSON structure to prevent syntax errors.
* The browser evaluates the manipulated JavaScript structure and executes the alert, successfully completing the laboratory.

## Conclusion
* This laboratory highlights a complex interaction where a server-side reflection flaw facilitates a client-side DOM XSS vulnerability.
* The utilization of the `eval()` function to parse JSON data is highly insecure and creates a direct sink for arbitrary code execution if the JSON structure can be manipulated via reflected input. Modern applications should exclusively utilize `JSON.parse()`.

## Related Common Weakness Enumerations (CWEs)

| CWE ID | Title | Description |
| :--- | :--- | :--- |
| CWE-79 | **Improper Neutralization of Input During Web Page Generation ('Cross-site Scripting')** | The vulnerability enabling the execution of injected scripts. |
| CWE-95 | **Improper Neutralization of Directives in Dynamically Evaluated Code ('Eval Injection')** | The specific misuse of the `eval()` function. |