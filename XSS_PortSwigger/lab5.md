# PortSwigger Lab Writeup: DOM XSS in innerHTML Sink Using source location.search

## Objective
The objective of this laboratory exercise is to exploit a DOM XSS vulnerability caused by insecure assignment of data from `location.search` (the source) to the `innerHTML` property (the sink).

* **Lab URL**: https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-innerhtml-sink
* **Category**: Cross-Site Scripting (DOM XSS)
* **Difficulty**: Apprentice

## Payloads Utilized
* **Payload (Event Handler Injection - Successful):**
  `<img src=1 onerror=alert(1)>`

## Exploitation Methodology

### Step 1: Application Observation
* Access the laboratory URL and perform a search using the provided search box.
* The application displays the search results on the page.

### Step 2: Identification of the Vulnerable Endpoint
* Inspect the page source to analyze the client-side JavaScript rendering the search results.
* The script extracts the search term from `window.location.search`.
* It then locates an element on the page (e.g., a `<span>` or `<div>` with a specific ID) and updates its content by directly assigning the search query to the `innerHTML` property.
* *Note:* Modern browsers restrict the execution of standard `<script>` tags when inserted via `innerHTML` for security reasons.

### Step 3: Payload Injection
* Because `<script>` tags are blocked by the browser when utilizing `innerHTML`, an alternative vector must be employed. Event handlers on standard HTML elements (such as `<img>` or `<iframe>`) bypass this restriction.
* Inject the payload `<img src=1 onerror=alert(1)>` into the search field.
* The client-side script assigns this payload directly to the `innerHTML` of the target element.
* The browser attempts to render the `<img>` tag. The `src=1` attribute points to an invalid image source.
* The resulting failure triggers the `onerror` event handler, executing the associated JavaScript `alert(1)`, completing the laboratory.

## Conclusion
* This laboratory highlights the dangers of the `innerHTML` sink. 
* While standard `<script>` tag injection is mitigated by browser security mechanisms, developers must be aware that attackers can easily bypass this by leveraging HTML event handlers (`onload`, `onerror`, `onmouseover`, etc.) to achieve cross-site scripting. Safe alternatives like `textContent` or `innerText` should be used when rendering untrusted text.