# Lab: User ID controlled by request parameter

## LAB DESC

```
This lab has a horizontal privilege escalation vulnerability on the user account page.

To solve the lab, obtain the API key for the user carlos and submit it as the solution.

You can log in to your own account using the following credentials: wiener:peter
```


## SOLUTION
All we gotta do is change this `https://0ae500d3043ab4dc80d8dbc3003100a7.web-security-academy.net/my-account?id=wiener` to `https://0ae500d3043ab4dc80d8dbc3003100a7.web-security-academy.net/my-account?id=carlos`, escalating horizontal privilege.
### API key of `carlos`: `HlWdGWDgA8XuvJsDNjkGG9DfiuocZxNc`

> LAB SOLVED!
