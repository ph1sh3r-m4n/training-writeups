# PortSwigger Lab Writeup: DOM XSS in AngularJS Expression with Angle Brackets and Double Quotes HTML-Encoded

## Objective
The objective of this laboratory exercise is to exploit a DOM XSS vulnerability within an AngularJS application. The application strictly HTML-encodes angle brackets (`<`, `>`) and double quotes (`"`), preventing standard HTML injection. The goal is to leverage AngularJS template expressions to execute an arbitrary JavaScript alert.

* **Lab URL**: https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-angularjs-expression
* **Category**: Cross-Site Scripting (DOM XSS)
* **Difficulty**: Practitioner

## Payloads Utilized
* **Payload (AngularJS Sandbox Escape/Execution - Successful):**
  `{{$on.constructor('alert(1)')()}}`

## Exploitation Methodology

### Step 1: Application Observation
* Access the laboratory URL and utilize the search functionality.
* The application reflects the search term on the page.

### Step 2: Identification of the Vulnerable Endpoint
* Inspect the page source to analyze the rendering context. 
* The application utilizes the AngularJS framework, identified by the presence of the `ng-app` attribute on the `<body>` element.
* The search term is reflected within the scope of this `ng-app` directive.
* Testing standard payloads (e.g., `<script>`) reveals that the server properly HTML-encodes angle brackets and double quotes, neutralizing traditional XSS vectors. 
* However, because the input is reflected within an active AngularJS application, double curly braces `{{ }}` can be utilized to execute AngularJS expressions.

### Step 3: Payload Injection
* AngularJS evaluates expressions placed within `{{ }}`. While AngularJS implements a sandbox to restrict JavaScript execution, vulnerabilities in expression parsing allow for sandbox escapes.
* Inject the payload `{{$on.constructor('alert(1)')()}}` into the search field.
* The server reflects the payload into the HTML response.
* The client-side AngularJS framework parses the document, identifies the `{{ }}` expression, and evaluates its contents.
* The `$on.constructor` property provides access to the global `Function` constructor, allowing the string `'alert(1)'` to be compiled into a functional JavaScript execution block, which is then invoked by the trailing `()`.
* The browser executes the alert function, successfully completing the laboratory.

## Conclusion
* This laboratory demonstrates that standard HTML encoding is insufficient when user input is reflected within a client-side template framework context.
* Attackers can leverage framework-specific syntax (like AngularJS expressions) to achieve arbitrary JavaScript execution without the need for traditional HTML injection.

## Related Common Weakness Enumerations (CWEs)

| CWE ID | Title | Description |
| :--- | :--- | :--- |
| CWE-79 | **Improper Neutralization of Input During Web Page Generation ('Cross-site Scripting')** | The core issue allowing expression injection. |
| CWE-94 | **Improper Control of Generation of Code ('Code Injection')** | Exploiting the expression engine to execute arbitrary code. |