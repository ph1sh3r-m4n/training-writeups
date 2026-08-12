# Lab: User ID controlled by request parameter with data leakage in redirect

## LAB DESC
```
This lab contains an access control vulnerability where sensitive information is leaked in the body of a redirect response.

To solve the lab, obtain the API key for the user carlos and submit it as the solution.

You can log in to your own account using the following credentials: wiener:peter
```
## SOLUTION

See, when you try to change the user by typing `carlos` in place of `wiener`, the website will direct us back to the login page but the response may contain some data which should be confidential.  

That is exactly what happened here, I when to `Burp Proxy` and found the response body where `carlos'` API key was leaked, took that out and submitted the solution!


### carlos' API key: `8BcHdWo68If8P4iPQEKggnwp2yndq25n`

> LAB SOLVED!
