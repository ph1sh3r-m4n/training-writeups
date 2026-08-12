# Lab: User role controlled by request parameter

## LAB DESC

```
This lab has an admin panel at /admin, which identifies administrators using a forgeable cookie.

Solve the lab by accessing the admin panel and using it to delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter
```
## SOLUTION

All I had to do was change the value of `isadmin` of the session cookie to `true` from `false`, then put the path `/admin`, gain admin access and delete the user `carlos`

> LAB SOLVED
