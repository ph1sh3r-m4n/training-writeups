# Lab: Forced OAuth profile linking

## LAB DESC

```
This lab gives you the option to attach a social media profile to your account so that you can log in via OAuth instead of using the normal username and password. Due to the insecure implementation of the OAuth flow by the client application, an attacker can manipulate this functionality to obtain access to other users' accounts.

To solve the lab, use a CSRF attack to attach your own social media profile to the admin user's account on the blog website, then access the admin panel and delete carlos.

The admin user will open anything you send from the exploit server and they always have an active session on the blog website.

You can log in to your own accounts using the following credentials:

Blog website account: wiener:peter
Social media profile: peter.wiener:hotdog
```

## SOLUTION

```text
Attacker's social account
          |
          | OAuth authorization code
          v
     STOLEN-CODE
          |
          | placed in iframe
          v
   Exploit Server
          |
          | victim visits exploit
          v
    Admin's Browser
          |
          | authenticated request
          v
/oauth-linking?code=STOLEN-CODE
          |
          | no OAuth state validation
          v
Admin blog account
          |
          | linked to
          v
Attacker's social account
          |
          | "Log in with social media"
          v
     Admin session
```

---

## Step 1 — Log in normally

While proxying through Burp Suite, open **My account**.

Initially, log in using the normal username/password form.

After logging in, notice that the application provides an option to:

> Attach a social profile

---

## Step 2 — Attach your social profile

Click **Attach a social profile**.

You are redirected to the OAuth provider.

Log in using your own social-media credentials and complete the OAuth flow.

You should eventually be redirected back to the blog.

At this point, your social-media account is linked to your own blog account.

---

## Step 3 — Verify the OAuth login

Log out of the blog.

Return to **My account** and select:

> Log in with social media

You should be logged in automatically.

This confirms that the social account has been successfully linked.

---

## Step 4 — Analyze the OAuth flow

Open:

**Burp Suite → Proxy → HTTP history**

Look at the requests generated when you attached the social profile.

Find the OAuth authorization request:

```http
GET /auth?client_id=...
```

Examine its parameters.

You should see a `redirect_uri` that causes the OAuth authorization code to be sent to:

```text
/oauth-linking
```

Most importantly, notice that the OAuth authorization request does **not** contain a:

```text
state
```

parameter.

### Why is this important?

The `state` parameter is normally used to bind an OAuth flow to the user's session and prevent an attacker from initiating an OAuth flow and forcing another user to complete it.

Because the application does not use `state` here, the OAuth linking request can be forced in another user's authenticated session.

---

## Step 5 — Obtain an unused OAuth authorization code

Turn on Burp interception.

Go back to the blog and select:

> Attach a social profile

Forward the requests until you reach:

```http
GET /oauth-linking?code=...
```

Right-click the request and select:

> Copy URL

The copied URL will look similar to:

```text
https://YOUR-LAB-ID.web-security-academy.net/oauth-linking?code=STOLEN-CODE
```

### Important

**Drop this request instead of forwarding it.**

This prevents the authorization code from being consumed.

OAuth authorization codes are normally short-lived and/or single-use, so the code needs to remain unused for the next stage of the attack.

---

## Step 6 — Log out

Turn off Burp interception and log out of your own blog account.

At this point, you have:

```text
Your social-media account
        |
        v
Unused OAuth authorization code
        |
        v
/oauth-linking?code=STOLEN-CODE
```

You have **not** obtained the admin's credentials.

---

## Step 7 — Create the exploit

Go to the PortSwigger exploit server.

Create an HTML page containing:

```html
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/oauth-linking?code=STOLEN-CODE"></iframe>
```

Replace:

```text
YOUR-LAB-ID
```

with your lab identifier and:

```text
STOLEN-CODE
```

with the OAuth authorization code you copied earlier.

The iframe automatically causes the victim's browser to request:

```text
/oauth-linking?code=STOLEN-CODE
```

---

## Step 8 — Deliver the exploit to the victim

Click:

> Deliver exploit to victim

In the PortSwigger lab, this simulates the **victim/admin visiting your malicious page**.

The exploit server is **not** the admin.

The important sequence is:

```text
Attacker
   |
   | creates malicious iframe
   v
Exploit Server
   |
   | victim visits page
   v
Admin's Browser
   |
   | browser automatically loads iframe
   v
/oauth-linking?code=STOLEN-CODE
```

The admin is already authenticated to the blog.

Therefore, when the browser makes the request, the blog application processes it in the context of the admin's authenticated session.

---

## Step 9 — The account-linking attack

This is the critical step.

The OAuth code represents **the attacker's social-media account**.

However, the request is being made from the **admin's authenticated browser**.

Because the application does not validate an OAuth `state` value, it fails to verify that the OAuth flow was originally initiated by the admin.

The application therefore performs the equivalent of:

```text
Current blog account:
        ADMIN

OAuth identity:
        ATTACKER'S SOCIAL ACCOUNT

Result:
        ADMIN <----> ATTACKER'S SOCIAL ACCOUNT
```

The attacker's social account is now linked to the admin's blog account.

---

## Step 10 — Log in as admin

Now return to the blog's login page.

Select:

> Log in with social media

The application looks up the social identity.

Instead of finding your original normal blog account, it now finds:

```text
Your social account
        |
        v
Admin blog account
```

The application therefore creates a session for the **admin account**.

You now have administrator access.

---

## Step 11 — Access the admin panel

After logging in through social media, navigate to:

```text
/admin
```

You should now have access to the administrator panel.

Delete the user:

```text
carlos
```

This completes the lab.

---

# Why Do We Get Admin Access?

The most important part of this lab is understanding **which account is being linked to which identity**.

Before the exploit:

```text
Your social account
        |
        v
Your blog account
```

After the victim/admin loads the exploit:

```text
Your social account
        |
        v
Admin blog account
```

You do **not** steal the administrator's social-media credentials.

Instead, you abuse the application's account-linking functionality so that:

> **Your OAuth identity becomes associated with the administrator's account.**

When you subsequently authenticate using your own social-media account, the application identifies it as belonging to the admin account and gives you an admin session.

---

# Vulnerability

The root cause is:

```text
OAuth account linking
        +
Missing state parameter
        +
No protection against CSRF
        =
Account-linking CSRF
```

The missing `state` parameter allows an attacker-controlled OAuth flow to be completed in another user's authenticated session.

---

# Attack Classification

This vulnerability can be described as:

* **OAuth account-linking CSRF**
* **OAuth CSRF**
* **Login CSRF**
* Potentially **account takeover**

The `<iframe>` itself is not the vulnerability.

It is simply the mechanism used to cause the victim's browser to make the request automatically.

---

# One-Minute Summary

```text
1. Log into your own blog account.
2. Link your own social-media account.
3. Start the linking process again.
4. Intercept /oauth-linking?code=...
5. Copy the URL.
6. Drop the request so the code remains unused.
7. Put the URL inside an iframe on the exploit server.
8. Deliver the exploit to the victim.
9. The victim is the admin and is already logged into the blog.
10. The admin's browser loads the iframe.
11. Your social account becomes linked to the admin's blog account.
12. Log out.
13. Choose "Log in with social media".
14. You are authenticated as admin.
15. Access /admin and delete carlos.
```

## Root Cause

The application fails to bind the OAuth authorization flow to the user's session using the `state` parameter.

A secure implementation should generate an unpredictable `state` value when starting the OAuth flow, store it in the user's session, and verify it when the OAuth callback is received.
