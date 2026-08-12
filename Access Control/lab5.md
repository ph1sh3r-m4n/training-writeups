# Lab: URL-based access control can be circumvented

## LAB DESC

```
This website has an unauthenticated admin panel at /admin, but a front-end system has been configured to block external access to that path. However, the back-end application is built on a framework that supports the X-Original-URL header.

To solve the lab, access the admin panel and delete the user carlos.
```
## SOLUTION

interrcept the admin panel request on Burp and add the url `/admin/delete` in `X-Original-URL` header to bypass the vuln, add `username=carlos` separately to solve the lab.

> LAB SOLVED
