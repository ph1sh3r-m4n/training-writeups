# Lab: Web shell upload via Content-Type restriction bypass
## LAB DESC
```
This lab contains a vulnerable image upload function. It attempts to prevent users from uploading unexpected file types, but relies on checking user-controllable input to verify this.

To solve the lab, upload a basic PHP web shell and use it to exfiltrate the contents of the file /home/carlos/secret. Submit this secret using the button provided in the lab banner.

You can log in to your own account using the following credentials: wiener:peter
```
## SOLUTION
So this file upload function does perform checks but it implicitly trust the value of `Content-Type` header.
This is the vuln, its programmed to accept `images/jpeg` or `images/png`.  
All we have to do this just change the `Content-Type` header to one of the above while still uploading the `malw.php` malicious PHP code as used in the first lab.  
Uploading this to the server with the forged content header upload our malware to the server.  

We just need to call `GET /files/avatars/malw.php HTTP/2` request to execute our malicious code and voila we get our secret!

> SECRET: ky5PzG8UcKa3ElbnyeEbyKhHJIFp1tvG

> LAB SOLVED
