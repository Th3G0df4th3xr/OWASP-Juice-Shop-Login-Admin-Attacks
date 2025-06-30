# ✅ TO-DO – Brute Force Admin Credentials

- [x] Launch Juice Shop container
- [x] Intercept login with Burp
- [x] Use Intruder with common password list
- [x] Confirm success with `admin@juice-sh.op`
- [ ] Add screenshot of response in Burp

# 🔐 Attack: Brute-Force Admin Credentials

## 🎯 Objective
Identify valid login credentials for the admin account by systematically trying known or weak passwords.

---

## 🧠 Background Knowledge

- **Brute Force Attack**: Repeatedly attempting different username/password combinations to gain unauthorized access.
- **Credential Stuffing**: Using leaked credentials from other breaches.
- **Rate Limiting Bypass**: If not present, allows unrestricted attempts.

---

## 🛠️ Technologies Involved

- OWASP Juice Shop (Node.js backend)
- Kali Linux Terminal
- Tools: `hydra`, `wfuzz`, `Burp Intruder`, or manual attempts
- Common password lists (e.g., `rockyou.txt`)

---

## 🧩 MITRE ATT&CK Mapping

- **T1110.001** – Brute Force: Password Guessing  
- **T1078** – Valid Accounts

---

## 📜 Step-by-Step Execution

### 🔹 1. Run the Juice Shop Container

```bash
docker run -d -p 3000:3000 bkimminich/juice-shop



2. Open the Login Page
Navigate to:

bash
Copy
Edit
http://localhost:3000/#/login
🔹 3. Target the Login Request
Use Burp Suite to intercept login attempts

Send the request to Intruder

Set payload positions for:

Username: admin@juice-sh.op

Password: use wordlist (rockyou.txt or custom)

🔹 4. Run Intruder Attack
Payload: Passwords list

Monitor response content lengths for successful login

🔹 5. Manual Alternative
Use a known password from Juice Shop default:

css
Copy
Edit
admin@juice-sh.op / admin123
Test in the login form.

✅ Success Criteria
You’re redirected to the homepage as admin@juice-sh.op

Scoreboard shows ✅ “Login with administrator account”

📸 POC

🛡️ Mitigation
Implement strong password policies

Enable account lockouts or CAPTCHA after multiple failures

Use multi-factor authentication (MFA)

Monitor and log failed login attempts

🔗 References
OWASP Brute Force

Juice Shop GitHub

rockyou.txt Wordlist

