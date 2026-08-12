# Lab: User role can be modified in user profile

## LAB DESC
```
This lab has an admin panel at /admin. It's only accessible to logged-in users with a roleid of 2.

Solve the lab by accessing the admin panel and using it to delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter
```
## SOLUTION

we gotta change the email, send that req to `Burp Repeater`, add `"roleid":2` to the json body, forward the request, access `/admin` to get admin privileges, and finally delete the user `carlos`.

> LAB SOLVED
