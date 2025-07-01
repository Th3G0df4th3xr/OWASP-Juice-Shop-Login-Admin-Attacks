Certainly. Below is a **professional, elongated, and well-structured version** of both the `README.md` and `TODO.md` for the **Local Storage Tampering – JWT Overwrite Exploit** method to solve the **Login Admin** challenge in OWASP Juice Shop. This version uses formal OffSec-style language and includes additional clarity, context, and technical expectations for each step.

---

## 📄 `README.md` – Local Storage Tampering: JWT Overwrite Exploit

````markdown
# 🛠️ Local Storage Tampering – JWT Overwrite Exploit

## 🎯 Objective

This attack demonstrates how client-side storage (i.e., `localStorage`) can be tampered with to escalate privileges. By manually overwriting the JWT token stored in the browser, we impersonate the `admin` user in OWASP Juice Shop and gain access to protected routes such as `/#/administration`, thereby solving the **"Login Admin"** challenge.

---

## 🧠 Attack Methodology

### 1️⃣ Log in as a Normal User
Authenticate with any non-admin user account using the UI. This ensures that a valid token is stored in the browser's `localStorage`.

### 2️⃣ Open Developer Tools
- Navigate to `Application` tab in Chrome DevTools (or equivalent).
- Expand `Local Storage` → `http://localhost:3000`.
- Identify the key `token` which holds the JWT string.

### 3️⃣ Decode the JWT
Use [jwt.io](https://jwt.io) or any JWT decoder to inspect the token. Observe the claims like:
```json
{
  "email": "user@example.com",
  "role": "customer"
}
````

### 4️⃣ Forge a Malicious JWT

Create a new token with elevated privileges:

```json
{
  "email": "admin@juice-sh.op",
  "role": "admin"
}
```

⚠️ You must preserve the original header structure and signing method (`alg`, `typ`). Either:

* Use a fake signature (if backend does not verify),
* Or replay a valid admin JWT (from another attack, like RS256 to HS256 downgrade).

### 5️⃣ Inject the Forged Token

Execute the following command in DevTools → Console:

```js
localStorage.setItem("token", "YOUR_FORGED_JWT_HERE");
```

### 6️⃣ Refresh and Access Admin Panel

Reload the page. Navigate to:

```
http://localhost:3000/#/administration
```

You should now see the admin dashboard.

---

## 🧰 Tools & Techniques Used

* Browser DevTools (`F12` → Application & Console)
* JWT Debugger ([jwt.io](https://jwt.io))
* Understanding of JWT structure and claims
* Optional: Burp Suite for additional inspection or header replacement

---

## ✅ Indicators of Successful Exploitation

| Indicator                                       | Outcome                      |
| ----------------------------------------------- | ---------------------------- |
| `token` in `localStorage` has `"role": "admin"` | Token tampering successful   |
| Admin UI (`/#/administration`) is accessible    | Privilege escalation worked  |
| Scoreboard shows "Login Admin" as solved        | Challenge is marked complete |

---

## 🔐 Mitigation Strategies

| Control                                             | Purpose                 |
| --------------------------------------------------- | ----------------------- |
| ✅ Always verify JWT signature server-side           | Prevent forged tokens   |
| ❌ Do not trust JWT claims (e.g., role) client-side  | Avoid tampering abuse   |
| 🔒 Securely sign JWTs with robust keys              | Prevent token forgery   |
| 🎯 Use server-side session validation or expiration | Improve session hygiene |

---

### ✍️ Author: SHajohn16

````

---

## 📋 `TODO.md` – Local Storage Tampering

```markdown
# ✅ TO-DO – Local Storage Tampering (Login Admin Challenge)

## 🔐 Initial Setup

- [x] Log in to Juice Shop as a regular user to obtain a valid JWT in `localStorage`
- [x] Open Developer Tools (`F12`) → `Application` tab → `Local Storage` → `http://localhost:3000`
- [x] Locate the key named `token` and copy its value for inspection

---

## 🧠 Token Analysis & Forgery

- [x] Decode the original JWT using [jwt.io](https://jwt.io)
- [x] Understand the payload claims (e.g., `email`, `role`)
- [x] Craft a new JWT payload with `email: admin@juice-sh.op` and `role: admin`
- [x] Either:
  - Forge a token if signature isn't verified, or
  - Replay a valid admin JWT from another exploit (e.g., RS256-HS256 downgrade)

---

## 🎯 Overwrite & Access Admin Panel

- [x] Use the browser console to overwrite the token:
  ```js
  localStorage.setItem("token", "YOUR_NEW_ADMIN_JWT");
````

* [x] Refresh the page and visit:

  ```
  http://localhost:3000/#/administration
  ```
* [x] Verify that the admin panel is accessible
* [x] Confirm that the "Login Admin" challenge is marked as solved in the scoreboard

---

## 📸 Documentation

* [ ] Capture screenshot of `localStorage` before and after token tampering
* [ ] Capture screenshot of the admin panel access
* [ ] Capture screenshot of scoreboard showing challenge solved

---

