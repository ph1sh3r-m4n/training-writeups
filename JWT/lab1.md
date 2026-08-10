# Lab: JWT authentication bypass via unverified signature

## LAB DESC

```
This lab uses a JWT-based mechanism for handling sessions. Due to implementation flaws, the server doesn't verify the signature of any JWTs that it receives.

To solve the lab, modify your session token to gain access to the admin panel at /admin, then delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter
```

## SOLUTION

Simple JWT manipulation, all we have to do is change `{"iss":"portswigger","exp":1786348524,"sub":"wiener"}` to `{"iss":"portswigger","exp":1786348524,"sub":"administrator"}` as the server does not verify the signature, so just manipulating the user is enough.  

We change the `GET` request to `/admin` which returns `200 OK` concluding that we got admin privileges. 
Now we send in the request `GET /admin/delete?username=carlos HTTP/2` which delets the user _Carlos_ and solves the lab.  

> LAB SOLVED
