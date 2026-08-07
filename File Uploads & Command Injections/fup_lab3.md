# Lab: Web shell upload via path traversal

## LAB DESC
```
This lab contains a vulnerable image upload function. The server is configured to prevent execution of user-supplied files, but this restriction can be bypassed by exploiting a secondary vulnerability.

To solve the lab, upload a basic PHP web shell and use it to exfiltrate the contents of the file /home/carlos/secret. Submit this secret using the button provided in the lab banner.

You can log in to your own account using the following credentials: wiener:peter
```
## SOLUTION
This doesn't allow execution at /my-account/avatar level, so we gotta do some _path traversal_ and store this malicious script in some other directory.

I modified the `Content-Disposition` header's attribute `filename="..%2fexploit.php"` to store it one directory above where execution might be allowed.  
As it turns out I was lucky enough for the script to get executed on the following GET request
```http
GET /files/avatars/../malw.php HTTP/2
```

> Secret: cOO7M4zz7WzEFta1TTVTmJyTGdft4DMT

> LAB SOLVED
