# PortSwigger Lab Writeup: DOM XSS in jQuery Selector Sink Using a hashchange Event

## Objective
The objective of this laboratory exercise is to exploit a DOM XSS vulnerability where the sink is the jQuery `$()` selector function, and the source is the URL hash (`location.hash`). The goal is to deliver an exploit to a victim that triggers an arbitrary JavaScript function (`print()`) in their browser.

* **Lab URL**: https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-jquery-selector-hash-component-sink
* **Category**: Cross-Site Scripting (DOM XSS)
* **Difficulty**: Apprentice

## Payloads Utilized
* **Payload (Iframe Delivery - Successful):**
  `<iframe src="https://YOUR-LAB-ID.web-security-academy.net/#<img src=1 onerror=print()>"></iframe>`

*(Note: Replace `YOUR-LAB-ID` with the specific laboratory instance identifier).*

## Exploitation Methodology

### Step 1: Application Observation
* Access the laboratory URL and inspect the home page functionality. 
* The page utilizes client-side routing or dynamic content loading based on the URL hash fragment (the portion of the URL following the `#` symbol).

### Step 2: Identification of the Vulnerable Endpoint
* Review the page source to analyze the client-side JavaScript.
* The script listens for the `hashchange` event: `$(window).on('hashchange', function() { ... })`.
* Upon detecting a hash change, the script extracts the hash value and passes it directly into the jQuery selector function: `var element = $(location.hash);`.
* Older versions of jQuery are vulnerable to XSS if untrusted input containing HTML tags is passed into the `$()` selector function, as it attempts to render the HTML.

### Step 3: Payload Injection and Delivery
* The vulnerability can be triggered locally by appending `#<img src=1 onerror=print()>` to the laboratory URL and pressing Enter. The browser evaluates the invalid image, triggering the `onerror` event and executing the `print()` function.
* To exploit a victim, the payload must be delivered via an external site. Access the provided Exploit Server.
* Construct an HTML payload containing an `iframe` that points to the vulnerable application with the malicious hash appended. 
* Input the following into the Exploit Server's body section:
  `<iframe src="https://YOUR-LAB-ID.web-security-academy.net/#<img src=1 onerror=print()>"></iframe>`
* Select "Deliver exploit to victim". The victim's browser will load the iframe, triggering the `hashchange` execution flow, evaluating the injected HTML via the jQuery selector sink, and executing the `print()` function to resolve the laboratory.

## Conclusion
* This laboratory illustrates the danger of passing untrusted input, specifically from `location.hash`, directly into the jQuery `$()` selector.
* It also demonstrates how DOM XSS vulnerabilities can be weaponized and delivered to victims using external hosting mechanisms like iframes.

## Related Common Weakness Enumerations (CWEs)

| CWE ID | Title | Description |
| :--- | :--- | :--- |
| CWE-79 | **Improper Neutralization of Input During Web Page Generation ('Cross-site Scripting')** | The vulnerability enabling the execution of injected scripts via the DOM. |