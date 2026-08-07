# Lab: Web shell upload via obfuscated file extension

## LAB DESC

```
This lab contains a vulnerable image upload function. Certain file extensions are blacklisted, but this defense can be bypassed using a classic obfuscation technique.

To solve the lab, upload a basic PHP web shell, then use it to exfiltrate the contents of the file /home/carlos/secret. Submit this secret using the button provided in the lab banner.

You can log in to your own account using the following credentials: wiener:peter
```
## SOLUTION

Obsfucate the filename parameter with `exploit.php%00.jpg`, it was mostly a trial and error with obsfucated extensions, this one worked!  

Rest of the procedure matches with the previous labs!

> Secret: 5R0jLpdY1tgZwEhj418LX7bkWi2ctp8W

> LAB SOLVED!
