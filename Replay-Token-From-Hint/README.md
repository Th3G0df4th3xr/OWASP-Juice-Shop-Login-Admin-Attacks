# 🎯 Replay Token from Challenge Hint – JWT-Based Privilege Escalation

## 🔍 Objective

This attack leverages leaked authentication tokens (JWTs) exposed within **scoreboard hints** or **application debug data** in OWASP Juice Shop. By replaying these tokens and injecting them into `localStorage`, an attacker can impersonate privileged users—specifically `admin@juice-sh.op`—and gain unauthorized access to restricted areas like the `/administration` route, completing the **“Login Admin”** challenge without credentials.

---

## ⚙️ Technology Stack Behind Juice Shop

| Component      | Description                                   |
|----------------|-----------------------------------------------|
| 🌐 Frontend     | Angular SPA (Single Page Application)         |
| 🔙 Backend      | Node.js + Express REST API                    |
| 🔐 Auth Layer   | JSON Web Tokens (JWT, RS256 algorithm)        |
| 📁 Storage      | `localStorage` used to persist JWT on client  |
| 🧪 UI Behavior  | Relies on token role (e.g., `admin`) to gate access to UI features |

---

## 🧠 Attack Methodology – Replay Token Flow

This attack simulates the real-world scenario where secrets (tokens) are exposed by insecure UI implementations. Juice Shop provides scoreboard hints that sometimes contain:

- Base64-encoded JWTs  
- Pre-forged JWT strings  
- Direct token-bearing URLs  

By replaying these tokens in the browser, the frontend recognizes the user as authenticated and displays privileged views.

---

## 🪜 Step-by-Step Execution Guide

### 1️⃣ Launch Juice Shop & Access Scoreboard

- Open: `http://localhost:3000`
- Navigate to **Scoreboard** (click the star icon in the navigation bar)
- Scroll through **challenge hints**
- Look for the **“Login Admin”** hint
  - It may contain:
    - A Base64 string
    - A clickable URL like:
      ```
      http://localhost:3000/#/administration?token=eyJhbGciOi...
      ```

🧠 **Note:** If a full JWT is provided, copy it. If it’s Base64, decode using [base64decode.org](https://www.base64decode.org/) or the `base64` CLI.

---

### 2️⃣ Inject Token into Browser Local Storage

- Open Chrome/Firefox DevTools (`F12`)
- Go to the **Application tab**
- Under **Local Storage**, select `http://localhost:3000`
- Locate or create the `token` key

#### 👉 Overwrite or Create Token Key:

Go to **Console tab** and execute:
```js
localStorage.setItem('token', '<REPLAYED_JWT_HERE>');
🧾 Expected Output: No console error. Token is now stored in localStorage.

3️⃣ Refresh the Page and Access Admin UI
Press F5 or manually refresh the page.

Navigate to:

bash
Copy
Edit
http://localhost:3000/#/administration
✅ If the token is valid and its payload contains:

json
Copy
Edit
{
  "email": "admin@juice-sh.op",
  "role": "admin"
}
...you should see the admin interface.

4️⃣ Validate Challenge Completion
Open the Scoreboard

Confirm that the challenge “Login Admin” is now marked as Solved ✅

🔍 Real-World Misconfigurations Abused
Vulnerability	Description
🔓 Token leakage	Tokens exposed in public UI hints
🔐 No token expiration enforcement	Leaked tokens remain valid
❌ No origin validation	Tokens accepted from local injection
🧪 Insecure frontend trust	App logic relies on role from JWT claim only

🔐 Mitigation Strategies
Control	Description
✅ Use short-lived tokens (exp claim)	Minimizes replay window
🔒 Don’t expose secrets in help/hint systems	Keep challenge scaffolding separate
🚫 Never trust localStorage content blindly	Validate JWT server-side
🧠 Enforce RBAC and scope verification	Don’t rely on client-provided claims

🧾 Notes for Offensive Security Practitioners
This method mirrors real-world attack chains where leaked tokens, hardcoded secrets, or dev-only hints are exposed in frontend logic or public interfaces. Replaying these tokens allows lateral movement or privilege escalation without credential brute-force or cryptographic bypass.

It's an excellent case study for token reuse, insecure debugging aids, and JWT handling mistakes in modern Single Page Applications.

✍️ Author: #SHajohn16


