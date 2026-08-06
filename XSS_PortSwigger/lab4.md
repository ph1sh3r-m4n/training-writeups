# PortSwigger Lab Writeup: DOM XSS in document.write Sink Using source location.search Inside a Select Element

## Objective
The objective of this laboratory exercise is to exploit a DOM XSS vulnerability where the sink `document.write` insecurely handles input from `location.search`. The payload must be crafted to break out of a `<select>` HTML element to execute the script.

* **Lab URL**: https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-document-write-sink-inside-select-element
* **Category**: Cross-Site Scripting (DOM XSS)
* **Difficulty**: Apprentice

## Payloads Utilized
* **Payload (DOM Manipulation - Successful):**
  `"></select><script>alert(1)</script>`

## Exploitation Methodology

### Step 1: Application Observation
* Access the laboratory URL, which features an e-commerce platform displaying products.
* Click on a product to view its details. Notice the "Check stock" feature, which utilizes a dropdown menu (a `<select>` element) to choose a store location.

### Step 2: Identification of the Vulnerable Endpoint
* Observe the URL, which includes a `storeId` parameter (e.g., `?productId=1&storeId=London`).
* Inspecting the page source reveals a client-side script that reads the `storeId` from `window.location.search`.
* The script utilizes `document.write` to dynamically generate an `<option>` element within a `<select>` dropdown, using the provided `storeId` to set the selected value.

### Step 3: Payload Injection
* To achieve execution, the payload must escape the `<option>` tag and, crucially, the surrounding `<select>` tag, as `<script>` tags will not execute if embedded directly inside a `<select>` element.
* Modify the `storeId` parameter in the URL with the payload: `"></select><script>alert(1)</script>`.
* When the script executes `document.write`, the constructed HTML becomes:
  `<option selected>"></select><script>alert(1)</script></option>`
* The injected `">` closes the currently open tag. The `</select>` tag explicitly closes the dropdown structure, allowing the `<script>` tag to render in a context where it can be executed.
* The browser parses the script block and executes the alert, completing the laboratory.

## Conclusion
* This laboratory emphasizes the importance of understanding HTML context in DOM XSS. 
* Successfully exploiting sinks like `document.write` requires a precise understanding of the surrounding DOM structure to ensure the injected payload is parsed as an executable element rather than inert content.