# PortSwigger Lab Writeup: DOM XSS in document.write Sink Using source location.search

## Objective
The objective of this laboratory exercise is to exploit a Document Object Model (DOM) based Cross-Site Scripting (XSS) vulnerability. The application utilizes client-side JavaScript that insecurely processes data from `location.search` (the source) and passes it to `document.write` (the sink). The goal is to execute an arbitrary JavaScript alert.

* **Lab URL**: https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-document-write-sink
* **Category**: Cross-Site Scripting (DOM XSS)
* **Difficulty**: Apprentice

## Payloads Utilized
* **Payload (DOM Manipulation - Successful):**
  `"><script>alert(1)</script>`

## Exploitation Methodology

### Step 1: Application Observation
* Access the laboratory URL and utilize the search functionality with a benign term (e.g., `test`).
* The application displays the search term on the page. Unlike standard reflected XSS, reviewing the page source reveals a client-side JavaScript block responsible for rendering the term.

### Step 2: Identification of the Vulnerable Endpoint
* Analyze the client-side JavaScript execution. The script extracts the search term from the URL query string (`window.location.search`).
* The script then uses `document.write('<img src="/resources/images/tracker.gif?searchTerms='+query+'">');` to embed an image tag dynamically based on the input.
* The source is `location.search` and the sink is `document.write`.

### Step 3: Payload Injection
* To execute a script, the attacker must first break out of the existing `<img>` tag structure constructed by `document.write`.
* Inject the payload `"><script>alert(1)</script>` into the search parameter.
* The JavaScript variable `query` is populated with the payload. When `document.write` executes, the resulting HTML string becomes:
  `<img src="/resources/images/tracker.gif?searchTerms="><script>alert(1)</script>">`
* The injected `">` closes the `src` attribute and the `<img>` tag prematurely, allowing the subsequent `<script>` tag to be parsed as an independent HTML element.
* The browser executes the alert function, successfully completing the laboratory.

## Conclusion
* This laboratory demonstrates a DOM-based XSS vulnerability where the flaw exists entirely within the client-side JavaScript code rather than the server's HTML response.
* Developers must avoid using dangerous sinks like `document.write` with untrusted data, or ensure proper sanitization/encoding is applied on the client-side before rendering.

## Related Common Weakness Enumerations (CWEs)

| CWE ID | Title | Description |
| :--- | :--- | :--- |
| CWE-79 | **Improper Neutralization of Input During Web Page Generation ('Cross-site Scripting')** | The core issue allowing DOM manipulation. |
| CWE-116 | **Improper Encoding or Escaping of Output** | Client-side failure to safely encode data before passing it to a sink. |