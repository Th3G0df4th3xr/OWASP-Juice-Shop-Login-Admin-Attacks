
"Structured Flow of All Attacks". This file will summarize each attack in your repository under a consistent format, justifying the name by offering a systematic, categorized, and step-by-step view for learners and auditors.

You should place this file at the root of your repository so it's easily visible as a guide to all attack folders.

📁 File: Structured-Flow-of-All-Attacks.md
markdown
Copy
Edit
# 🧭 Structured Flow of All Attacks – OWASP Juice Shop Admin Login Exploitation

This document provides a consolidated, **structured walkthrough of all attack techniques** used to bypass authentication and gain admin access on OWASP Juice Shop.

Each section below corresponds to a documented attack folder in this repository, summarizing the **attack strategy, tools, logic, and exploitation flow** for quick reference and practice.

---

## 🔓 1. Broken Access Control

**Goal:** Access admin areas (e.g., `/administration`) without valid privileges.

- 🔍 **Technique:** Manually visit restricted routes
- 🧰 **Tool:** Browser / Burp / DevTools
- 📜 **Flow:**
  1. Log in as a normal user.
  2. Access `/#/administration` directly.
  3. If role-based access is not enforced, admin panel opens.

---

## 🧪 2. Brute Force Admin Credentials

**Goal:** Log in as admin using weak or leaked credentials.

- 🔍 **Technique:** Credential stuffing / brute-forcing login
- 🧰 **Tool:** Hydra / Burp Intruder / curl
- 📜 **Flow:**
  1. Try usernames like `admin@juice-sh.op`
  2. Use common passwords (`admin123`, `123456`)
  3. Check for response code differences to detect success

---

## 🔁 3. BurpSuite Response Manipulation

**Goal:** Modify server response to change roles/tokens.

- 🔍 **Technique:** Intercept login response and edit it
- 🧰 **Tool:** Burp Suite
- 📜 **Flow:**
  1. Login with any account using Burp Proxy
  2. Intercept and edit response token payload (e.g., role = admin)
  3. Forward and access restricted pages

---

## 🧑‍💻 4. Exposed Admin API

**Goal:** Access admin routes via exposed API endpoints.

- 🔍 **Technique:** Direct API calls to `POST /rest/user/login`
- 🧰 **Tool:** curl / Postman / Burp
- 📜 **Flow:**
  1. Analyze API endpoints
  2. Use valid credentials via POST call
  3. Extract and reuse JWT for login

---

## 🧩 5. JWT Token Manipulation

**Goal:** Modify a valid JWT to escalate privileges.

- 🔍 **Technique:** Change `role` or `email` in JWT payload
- 🧰 **Tool:** `jwt_tool.py`, jwt.io
- 📜 **Flow:**
  1. Decode token, edit payload (`"role":"admin"`)
  2. Re-encode and replace token
  3. Access admin features

---

## ⚠️ 6. JWT None Algorithm Exploit

**Goal:** Bypass JWT signature verification with `alg: none`

- 🔍 **Technique:** Replace algorithm header with `none`
- 🧰 **Tool:** jwt_tool / Burp / curl
- 📜 **Flow:**
  1. Intercept JWT and set `"alg": "none"`
  2. Remove signature portion
  3. Use unsigned token to gain access

---

## 🔀 7. JWT RS256-to-HS256 Downgrade

**Goal:** Exploit signature verification confusion between asymmetric and symmetric algorithms

- 🔍 **Technique:** Replace RS256 with HS256 and use public key as secret
- 🧰 **Tool:** jwt_tool / OpenSSL
- 📜 **Flow:**
  1. Get public key from server
  2. Change algorithm to HS256
  3. Sign with public key → use as HMAC key

---

## 💾 8. Local Storage Tampering

**Goal:** Overwrite token in browser storage manually.

- 🔍 **Technique:** Edit `localStorage.token`
- 🧰 **Tool:** Browser DevTools
- 📜 **Flow:**
  1. Log in, open Developer Tools → Application tab
  2. Modify JWT token in `localStorage`
  3. Reload page to apply session changes

---

## 📦 9. Replay Token from Hint

**Goal:** Use hardcoded or hinted token to gain admin access.

- 🔍 **Technique:** Token reuse from challenges
- 🧰 **Tool:** Inspect page source, hints, or scoreboard
- 📜 **Flow:**
  1. Locate token via hint
  2. Replace existing JWT with hinted token
  3. Verify elevated access

---

## 🔐 10. Session Fixation via JWT

**Goal:** Force victim to reuse a known JWT token.

- 🔍 **Technique:** Set token before login → session persists
- 🧰 **Tool:** Burp / curl / JavaScript
- 📜 **Flow:**
  1. Generate JWT as attacker
  2. Inject it into victim's browser (via XSS or social engineering)
  3. Victim logs in → attacker reuses same token

---

## 🧨 11. SQL Injection on Login

**Goal:** Bypass login using classic SQLi payloads

- 🔍 **Technique:** `' OR 1=1 --`
- 🧰 **Tool:** Browser / Burp / SQLMap (optional)
- 📜 **Flow:**
  1. Use payload in username field:
     ```
     ' OR 1=1 --
     ```
  2. Submit with blank or dummy password
  3. Gain access if vulnerable

---

## 🧃 12. Weak Password Reset Token Abuse

**Goal:** Predict or reuse weak reset token to reset admin password

- 🔍 **Technique:** Reset token guessing or reuse
- 🧰 **Tool:** Burp / JavaScript source inspection
- 📜 **Flow:**
  1. Trigger reset for `admin@juice-sh.op`
  2. Inspect token or predict token via patterns
  3. Reset password and log in as admin

---

## 🧾 Repository Navigation Guide

Each folder in this repository includes:

- `README.md` → Detailed attack description
- `TO-DO.md` → Checklist for practical steps
- `POC` → Optional screenshots or logs of successful exploitation

---

## 📌 Final Note

This structured format is designed to help pentesters, learners, and CTF players **systematically master OWASP Juice Shop admin account exploitation scenarios**.

> “Understanding the attacker’s mindset begins with mastering structured exploitation.”

---

