````markdown
# 🧪 Attack Lab: Burp Suite – Response Manipulation via JWT Tampering

## 🎯 Objective

Exploit weak client-side trust in HTTP **responses** by intercepting and modifying the server's role assignment mechanism. The goal is to **escalate privileges** by modifying tokens or role flags during response transit, gaining unauthorized access to restricted **admin-only** functionalities in a vulnerable web application.

---

## 🧠 Required Knowledge

- 🔍 **HTTP Response Anatomy**: Understanding headers (`Set-Cookie`, `Authorization`, `Cache-Control`) and payload structures (JSON, HTML, JWTs).
- 🛡 **RBAC Weaknesses**: Misconfigured access control mechanisms, absence of server-side role validation, and insecure trust models in frontend clients.
- 🧰 **Burp Suite Modules**:  
  - **Proxy**: Intercepts raw HTTP requests/responses in transit.  
  - **HTTP History**: Audit and replay recorded traffic.  
  - **Repeater**: Manual testing of modified requests/responses.  
  - **Decoder/Comparer**: JWT token manipulation, base64 decode/encode operations.

---

## 🛠️ Tools & Stack

| Tool | Purpose |
|------|---------|
| **Burp Suite (Community/Pro)** | Intercept/modify HTTP traffic |
| **OWASP Juice Shop** | Deliberately vulnerable Node.js app |
| **Docker** | Containerized lab deployment |
| **Firefox/Chromium DevTools** | Token inspection, storage manipulation |
| **JWT.io / CyberChef** | Decode & manipulate JWT tokens |

---

## 🧩 MITRE ATT&CK Mapping

| Technique | Description |
|----------|-------------|
| **T1556.004** | *Input Capture: Application Layer Protocols* – Manipulate web-layer responses to inject forged session tokens or escalate roles. |
| **T1078** | *Valid Accounts* – Gain access to admin functionality using session hijacking or JWT escalation. |

---

## ⚙️ Environment Setup

### 🔹 1. Deploy Juice Shop (Dockerized)

```bash
docker run -d -p 3000:3000 bkimminich/juice-shop
````

* `-d`: Run in detached mode.
* `-p 3000:3000`: Map container port to host port for access at `http://localhost:3000`.

---

## 🧪 Attack Workflow

### 🔸 2. Intercept Login Response

1. Launch Burp Suite with Proxy enabled.
2. Configure your browser to route traffic through Burp (e.g., 127.0.0.1:8080).
3. Register a **non-admin user** in Juice Shop.
4. Log in and **capture the HTTP response** to the `POST /rest/user/login` endpoint.

   Look for a response containing:

   ```json
   {
     "authentication": {
       "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
       "bid": 123,
       "role": "customer"
     }
   }
   ```

### 🔸 3. Modify the Role Field

In Burp:

* Forward the intercepted response to **Repeater**.
* Edit the response body, replacing:

  ```json
  "role": "customer"
  ```

  with:

  ```json
  "role": "admin"
  ```
* **Reissue** the modified response to the browser (or intercept in Proxy and forward).

---

### 🔸 4. Persist the Tampered Token

Use DevTools (F12) in your browser:

* Navigate to `Application → LocalStorage` or `SessionStorage`.
* Replace the original JWT token with your **forged token** from the modified response.
* Refresh the app – verify if admin panels or protected features become visible.

---

### 🔸 5. Validate Escalation

* Try accessing `/administration` or delete a user account.
* Confirm that admin-only APIs (`DELETE /rest/user/{id}`, etc.) are now permitted with your modified token.

---

## 🔬 Post-Exploitation Observations

* **JWT Trust Model Flaw**: If the client trusts the `role` field from the response and doesn't re-validate on each API request, privilege escalation succeeds.
* **Lack of Server-Side Role Checks**: APIs should validate token claims via a trusted backend source, not just client-passed values.
* **Secure Flag Misuse**: If cookies/tokens lack `HttpOnly` and `Secure`, attackers can manipulate via JavaScript or insecure channels.

---

## 🛡️ Mitigation Strategies

| Vector                  | Mitigation                                                                 |
| ----------------------- | -------------------------------------------------------------------------- |
| Client-side trust       | Enforce role validation on the server for each request                     |
| JWT tampering           | Implement JWT signing and verification using secret keys with short expiry |
| Interceptable responses | Enforce HTTPS with HSTS, Content Security Policy                           |
| Browser storage         | Avoid storing auth tokens in LocalStorage; prefer `HttpOnly` cookies       |

---

## 🧠 Final Thoughts

This lab exploits a fundamental design flaw: trusting client-side control over authentication or role information. In real-world apps, this vector is commonly seen in:

* Single Page Applications (SPAs) using JWTs.
* Legacy web apps without proper access control enforcement.
* APIs trusting modified frontend states (like `isAdmin` flags).

For red teamers and AppSec professionals, such attacks highlight the importance of **response-level validation**, **JWT integrity assurance**, and the **principle of least privilege** in session design.

```
```
