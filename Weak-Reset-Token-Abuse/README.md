
OffSec-style README.md for the 🔓 Weak Reset Token Abuse challenge in OWASP Juice Shop — covering deep technical flow, exploited vulnerability, attack steps with precise guidance, tools, expected outputs, and mitigation insights.

markdown
Copy
Edit
# 🔓 Weak Reset Token Abuse  
🎯 Challenge: Unauthorized Password Reset of Admin User  
🧠 Category: Business Logic Abuse / Token Guessing  
✍️ Author: SHajohn16  

---

## 🎯 Objective

Exploit the use of **weak or predictable password reset tokens** in OWASP Juice Shop to perform an **unauthorized password reset for a privileged account** (e.g., `admin@juice-sh.op`) without needing access to the email account.

This attack highlights the security flaws in token-based recovery flows where tokens are either:
- Not cryptographically secure,
- Guessable,
- Client-side predictable (e.g., exposed in frontend JavaScript), or
- Leaked through insecure endpoints.

---

## ⚙️ Technologies & Stack Involved

| Component            | Description                                             |
|----------------------|---------------------------------------------------------|
| 🔐 Reset Token Format | Base64 / UUID / predictable hash exposed in JS         |
| 🧩 Frontend Logic     | Angular SPA, JavaScript (`main.*.js`)                  |
| 🔙 Backend API        | Node.js Express + REST endpoints for reset             |
| 🧰 Tools              | Burp Suite / Proxy / Browser DevTools / Token Decoder  |
| 📁 Storage Layer      | Local reset tokens or exposed via URL                  |

---

## 🧠 Attack Methodology Overview

The attack targets the **password recovery workflow**, where a user requests a reset token via email. If the token generation mechanism is weak or improperly exposed:
- The token can be guessed, leaked, or extracted from client-side logic.
- The attacker can use this token manually to reset another user’s password.
- No email access is required.

---

## 🪜 Step-by-Step Exploit Flow

### 1️⃣ Initiate Admin Password Reset

- Visit `http://localhost:3000/#/forgot-password`
- In the **email field**, enter:
admin@juice-sh.op

yaml
Copy
Edit
- Click **“Request Password Reset”**

✅ *Expected Output:* A message appears confirming the reset request was sent.

---

### 2️⃣ Investigate Token Generation or Exposure

There are three possible techniques to discover the reset token:

#### 🔍 A. JavaScript Inspection:
- Open DevTools → **Sources → main.*.js**
- Search for hardcoded reset token logic (e.g., static UUIDs, predictable sequences)

#### 🔍 B. Check URL Parameters:
- After clicking reset link (or via scoreboard hints), look for token passed as:
/#/reset-password/<token>

markdown
Copy
Edit

#### 🔍 C. Proxy Interception:
- Use **Burp Suite** to monitor the reset token API call
- Look for endpoint like:
POST /rest/user/reset-password

yaml
Copy
Edit

✅ *Expected Output:* You identify or extract the reset token value for admin.

---

### 3️⃣ Access Password Reset Form with Captured Token

- Manually navigate to:
http://localhost:3000/#/reset-password/<captured_token>

yaml
Copy
Edit
- Enter a new password (e.g., `P@ssword123`)
- Submit the form

✅ *Expected Output:* Password successfully reset for `admin@juice-sh.op`

---

### 4️⃣ Log in with New Credentials

- Visit `http://localhost:3000/#/login`
- Use:
- **Email**: `admin@juice-sh.op`
- **Password**: The newly set password

✅ *Expected Output:* Logged in as admin. Navigate to `/#/administration` and check the **Scoreboard** — “Reset Admin Password” challenge is now **marked as solved**.

---

## 🔍 Misconfigurations Exploited

| Vulnerability               | Impact                                             |
|-----------------------------|----------------------------------------------------|
| ❌ Weak / Predictable Token  | Tokens guessed or exposed from client-side code    |
| ❌ Lack of Expiry            | Tokens remain valid too long                       |
| ❌ No Token Binding          | Tokens not bound to IP/UA/session context          |
| ❌ Lack of Logging           | No alert for multiple invalid token attempts       |

---

## ✅ Indicators of Success

| Action                                | Expected Result                          |
|---------------------------------------|-------------------------------------------|
| Visiting reset page with token        | Reset form is rendered                    |
| Submitting new password               | App confirms password changed             |
| Logging in as admin                   | Admin dashboard becomes accessible        |
| Scoreboard status                     | Challenge “Reset Admin Password” is ✅     |

---

## 🛡️ Mitigation Strategies

| Control                                | Recommended Fix                           |
|----------------------------------------|-------------------------------------------|
| 🔒 Secure Random Tokens                | Use cryptographically strong UUIDs        |
| ⏱️ Expiry Time Enforcement              | Enforce short TTL on reset tokens         |
| 🧬 Token Binding                       | Bind tokens to IP or User-Agent           |
| 🔄 Invalidate on Use                   | One-time-use tokens with server revocation|
| 📜 Logging + Alerts                    | Monitor multiple reset requests per user  |

---

## 💡 Real-World Implications

This scenario reflects a real-world class of vulnerabilities where password reset mechanisms are **implemented insecurely**, allowing attackers to bypass the need for email access and directly compromise accounts using flawed logic or token handling.

These vulnerabilities are often observed in:
- Single Page Applications (SPAs)
- Mobile app APIs with exposed reset logic
- Poorly audited legacy authentication modules

---

## 🧾 References

- [OWASP Forgot Password Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Forgot_Password_Cheat_Sheet.html)
- [OWASP Top 10 – A07: Identification and Authentication Failures](https://owasp.org/Top10/A07_2021-Identification_and_Authentication_Failures/)
- [Juice Shop GitHub Source](https://github.com/juice-shop/juice-shop)

---


