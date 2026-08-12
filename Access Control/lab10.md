# Lab: User ID controlled by request parameter with password disclosure

## LAB DESC

```
This lab has user account page that contains the current user's existing password, prefilled in a masked input.

To solve the lab, retrieve the administrator's password, then use it to delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter
```
## SOLUTION

change name in id from `wiener` to `administrator`, then go to the response body in `Burp Proxy`, and then copy the admin's password which was `0u3o4dm2k8if5op1i7m0` and login as admin.  
After logging in, visit the admin panel and  then delete the user `carlos`.
> LAB SOLVED
