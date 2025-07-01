Absolutely. Below is a **complete, structured, and OffSec-style breakdown** of the **"Exposed Admin API"** attack method to solve the **"Login Admin"** challenge in **OWASP Juice Shop**, using the **unauthenticated admin REST endpoint**. This walkthrough includes:

---

### ✅ Scope: `Exposed-Admin-API/` Folder

### 🎯 Challenge: OWASP Juice Shop – *Login Admin*

### 💥 Technique: **Unauthenticated Access to Admin API**

### 📌 Objective: Access the admin dashboard or impersonate the admin user via unprotected admin functionality.

---

## 📦 Technology Stack Behind Juice Shop

| Component         | Details                                                                |
| ----------------- | ---------------------------------------------------------------------- |
| 🖥️ Frontend      | Angular Single Page Application                                        |
| 🌐 Backend        | Node.js (Express.js REST API)                                          |
| 🔐 Auth Mechanism | JWT (RS256) based session management                                   |
| 📄 API Design     | RESTful endpoints; some exposed at `/rest/user/`, `/rest/admin/`, etc. |
| 🗄️ Database      | SQLite                                                                 |

---

## ⚔️ Attack Summary

> Juice Shop exposes internal API routes like `/api/Administration` and `/rest/user/login` which are **not properly protected** by authentication or role-based access control.

By discovering and interacting with these endpoints using **Burp Suite**, browser Dev Tools, or direct `curl`/`httpie` requests, you can:

* **Trigger login functionality as the admin**
* **Bypass UI restrictions**
* **Achieve challenge completion without credentials**

---

## 🧭 Attack Flow (High-Level)

```text
1. 🔍 Discover exposed admin-related endpoints (e.g., /rest/user/login)
2. 🎯 Target the unauthenticated /rest/user/login API
3. 🧪 Submit a login request directly as admin@juice-sh.op via API
4. 🪙 Capture the JWT returned in the response
5. 🔐 Inject the token into browser's localStorage or use via Burp
6. 🛡️ Gain access to admin UI and trigger "Login Admin" challenge success
```

---

## 🧪 Deep-Dive Methodology (Step-by-Step)

---

### 1️⃣ Discovering the Admin Login API Endpoint

#### 🔎 Investigation Step

* Open **DevTools > Network Tab**
* Trigger a login attempt in the web UI
* Observe the request made to:

```
POST /rest/user/login
```

This is the **authentication API** used by the frontend.

#### 🔥 Why It Matters:

You can interact with this endpoint **without using the UI**, and it doesn't enforce additional client-side logic like captcha or rate limiting.

---

### 2️⃣ Use `curl` or `httpie` to Send Raw Login Payload

You can bypass the UI and send a direct HTTP POST request to log in as **admin**:

#### 🧪 Command:

```bash
curl -X POST http://localhost:3000/rest/user/login \
  -H "Content-Type: application/json" \
  -d '{"email":"admin@juice-sh.op","password":"admin123"}'
```

#### 📋 Expected Output:

```json
{
  "authentication": {
    "token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
    "bid": "some-session-id"
  },
  "user": {
    "email": "admin@juice-sh.op",
    "role": "admin",
    ...
  }
}
```

> ✅ **Token Successfully Obtained**

---

### 3️⃣ Manually Inject JWT Token into the App

#### 🧰 Option A: Browser LocalStorage Injection

* Open browser > DevTools > Application > LocalStorage
* Navigate to `http://localhost:3000`
* Set:

  ```js
  localStorage.token = "eyJh..."; // Your JWT
  ```
* Refresh the page.

#### 🧰 Option B: Burp Suite Header Replacement

* Intercept any request.
* Add header:

  ```
  Authorization: Bearer <your_JWT_token>
  ```

---

### 4️⃣ Verify Admin Access

* Go to the Juice Shop menu → Account → Order History
* You should now be logged in as **[admin@juice-sh.op](mailto:admin@juice-sh.op)**
* Navigate to **Score Board**
* ✅ Challenge **"Login Admin"** should now be marked as **solved**

---

## 📂 File Structure for `Exposed-Admin-API/` Folder

```bash
Exposed-Admin-API/
├── curl_login_as_admin.sh     # Scripted API login & JWT extraction
├── token.txt                  # Output of successful login (JWT token)
├── screenshots/               # (Optional) Burp + UI proof of login
├── README.md                  # Full technical walkthrough
├── TODO.md                    # Task checklist
└── extended_description.txt   # Short attack abstract
```

---

## 🛡️ Why This Works (Technical Insight)

* The backend fails to restrict `/rest/user/login` from being called outside the Angular UI.
* It doesn’t validate origin headers, CSRF tokens, or rate limits.
* Juice Shop exposes full admin access based solely on possession of a JWT for `admin@juice-sh.op`.

---

## 🧩 Security Misconfigurations Exploited

| Issue                         | Description                                       |
| ----------------------------- | ------------------------------------------------- |
| 🔓 Insecure API Exposure      | Login functionality exposed via raw HTTP endpoint |
| 🚫 No RBAC                    | No check on who can call the endpoint             |
| 🧪 Weak Authentication Design | Login protected only by simple credentials        |
| 🎯 JWT-Based Auth             | Token is reusable and valid for direct injection  |

---

## 📌 Final Outcome

* Access the application as `admin@juice-sh.op`
* Solve **"Login Admin"** challenge
* Demonstrate how **unauthenticated API misuse** can fully bypass UI-layer protections

---

