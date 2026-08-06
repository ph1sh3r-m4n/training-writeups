# PortSwigger Lab Writeup: Stored DOM XSS

## Objective
The objective of this laboratory exercise is to exploit a stored DOM XSS vulnerability. The application attempts to sanitize stored user input on the client-side before rendering it via `innerHTML`. However, the sanitization mechanism is flawed. The goal is to bypass the sanitization and execute an arbitrary JavaScript alert.

* **Lab URL**: https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-dom-xss-stored
* **Category**: Cross-Site Scripting (DOM XSS)
* **Difficulty**: Practitioner

## Payloads Utilized
* **Payload (Sanitization Bypass - Successful):**
  `<><img src=1 onerror=alert(1)>`

## Exploitation Methodology

### Step 1: Application Observation
* Access the laboratory URL, which hosts a blog platform.
* Navigate to an individual blog post and observe the comment section. 
* Submit a benign test comment to analyze the application's behavior.

### Step 2: Identification of the Vulnerable Endpoint
* Inspect the page source to analyze the client-side JavaScript responsible for rendering comments.
* The script retrieves comments from the server and passes them through an `escapeHTML` function before inserting them into the DOM using the `innerHTML` sink.
* Analyze the `escapeHTML` function logic. The function utilizes the JavaScript `replace()` method to neutralize angle brackets: `comment.replace('<', '&lt;')`.
* The critical flaw lies in the utilization of the `replace()` method with a string argument instead of a regular expression with the global flag (`/g`). When provided a string, `replace()` only alters the *first* occurrence of the target character.

### Step 3: Payload Injection
* To exploit this logic flaw, an attacker must provide an initial angle bracket to be consumed by the faulty sanitization function, leaving subsequent malicious HTML tags intact.
* Submit a new comment containing the payload: `<><img src=1 onerror=alert(1)>`. Provide arbitrary data for the other required fields.
* Upon rendering, the client-side script processes the payload. The `escapeHTML` function replaces the very first `<` character. 
* The resulting string assigned to `innerHTML` becomes:
  `&lt;><img src=1 onerror=alert(1)>`
* The subsequent `<img>` tag remains untouched. The browser parses the `innerHTML` assignment, attempts to load the invalid image source, and triggers the `onerror` event handler.
* The associated JavaScript `alert(1)` is executed, successfully completing the laboratory exercise.

## Conclusion
* This laboratory demonstrates that client-side sanitization routines must be implemented comprehensively. 
* Relying on native string replacement functions without global matching flags results in partial sanitization, allowing attackers to trivially bypass the security controls and execute stored DOM XSS attacks.

## Related Common Weakness Enumerations (CWEs)

| CWE ID | Title | Description |
| :--- | :--- | :--- |
| CWE-79 | **Improper Neutralization of Input During Web Page Generation ('Cross-site Scripting')** | The vulnerability enabling the execution of injected scripts via the DOM. |
| CWE-116 | **Improper Encoding or Escaping of Output** | The flawed client-side implementation of output escaping. |