# PortSwigger Lab Writeup: DOM XSS in jQuery Anchor href Attribute Sink Using location.search Source

## Objective
The objective of this laboratory exercise is to exploit a Document Object Model (DOM) based Cross-Site Scripting (XSS) vulnerability. The application utilizes client-side jQuery to extract data from the URL query string (`location.search`) and insecurely assigns it to the `href` attribute of an anchor element. The goal is to manipulate the anchor link to execute arbitrary JavaScript when a user interacts with it.

* **Lab URL**: https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-jquery-href-attribute-sink
* **Category**: Cross-Site Scripting (DOM XSS)
* **Difficulty**: Apprentice

## Payloads Utilized
* **Payload (JavaScript Pseudo-protocol - Successful):**
  `javascript:alert(1)`

## Exploitation Methodology

### Step 1: Application Observation
* Access the laboratory URL and navigate to the "Submit feedback" page.
* Observe the URL, which includes a `returnPath` parameter (e.g., `?returnPath=/`). 
* The page features a "Back" button intended to return the user to the previous page.

### Step 2: Identification of the Vulnerable Endpoint
* Inspect the page source to analyze the client-side JavaScript handling the "Back" button functionality.
* The script utilizes jQuery to locate the anchor element: `$('#backLink')`.
* It extracts the value of the `returnPath` parameter using `URLSearchParams(window.location.search).get('returnPath')`.
* The script then insecurely assigns this extracted value directly to the `href` attribute of the anchor element using the jQuery `attr()` function.

### Step 3: Payload Injection
* To execute JavaScript via an `href` attribute, the `javascript:` pseudo-protocol must be utilized.
* Modify the `returnPath` parameter in the URL with the payload: `?returnPath=javascript:alert(1)`.
* When the page loads, the jQuery script executes, dynamically updating the "Back" button's HTML to:
  `<a id="backLink" href="javascript:alert(1)">Back</a>`
* Click the "Back" button on the webpage. The browser attempts to navigate to the specified URL, subsequently executing the injected `alert(1)` function and completing the laboratory.

## Conclusion
* This laboratory demonstrates the risk associated with dynamically setting URL attributes (`href`, `src`, `action`) using untrusted input.
* While the input is not executed immediately upon page load, manipulating the DOM to insert a `javascript:` URI creates a persistent XSS vector triggered upon user interaction.

## Related Common Weakness Enumerations (CWEs)

| CWE ID | Title | Description |
| :--- | :--- | :--- |
| CWE-79 | **Improper Neutralization of Input During Web Page Generation ('Cross-site Scripting')** | The core issue allowing DOM manipulation. |
| CWE-74 | **Improper Neutralization of Special Elements in Output Used by a Downstream Component ('Injection')** | Injection into the href attribute. |