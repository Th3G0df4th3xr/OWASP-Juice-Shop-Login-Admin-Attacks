
Weak Reset Token Abuse challenge in OWASP Juice Shop, written in a structured, professional, and OffSec-style format:

markdown
Copy
Edit
# ✅ TO-DO – Weak Reset Token Abuse  
🎯 Challenge: *Reset Admin Password*  
🛠️ Technique: Token Guessing / Token Reuse  
✍️ Author: SHajohn16  

---

## 🧪 Phase 1 – Environment Setup

- [x] Launch OWASP Juice Shop locally (`http://localhost:3000`)
- [x] Open Developer Tools or prepare Burp Suite proxy
- [x] Ensure JavaScript files are accessible under `Sources` tab

✅ *Outcome:* Ready to intercept or analyze password reset behavior.

---

## 📤 Phase 2 – Trigger Password Reset for Admin

- [x] Navigate to `/#/forgot-password`
- [x] Enter the email address:  
admin@juice-sh.op

yaml
Copy
Edit
- [x] Click **Request Password Reset**

✅ *Outcome:* Confirmation banner appears indicating a reset email was "sent".

---

## 🔍 Phase 3 – Locate or Predict Reset Token

Choose any applicable method to retrieve the reset token:

- [x] Inspect `main.*.js` in DevTools → Sources  
- Search for hardcoded token strings or reset logic
- [x] Check scoreboard hints for reset token leaks
- [x] Intercept the request/response using Burp Suite or browser dev tools
- [x] Confirm token is passed in a reset link format:
http://localhost:3000/#/reset-password/<token>

yaml
Copy
Edit

✅ *Outcome:* A reset token is discovered for the admin account.

---

## 🔐 Phase 4 – Submit New Password with Retrieved Token

- [x] Manually visit the reset URL using discovered token:
/#/reset-password/<admin_token>

yaml
Copy
Edit
- [x] Enter a new password (e.g., `P@ssword123`)
- [x] Submit the form

✅ *Outcome:* Password successfully reset for `admin@juice-sh.op`.

---

## 🔓 Phase 5 – Validate Access

- [x] Navigate to `/#/login`
- [x] Log in with:
- **Email**: `admin@juice-sh.op`
- **Password**: `P@ssword123`
- [x] Visit `/administration` to verify access
- [x] Open scoreboard and check challenge status

✅ *Outcome:* Admin dashboard accessible; challenge marked as solved ✅.

---

## 📸 Phase 6 – Documentation & Evidence

- [ ] Screenshot of the token found (DevTools or intercepted)
- [ ] Screenshot of the reset-password URL with token
- [ ] Screenshot showing new password set confirmation
- [ ] Screenshot of admin panel access
- [ ] Screenshot of scoreboard challenge marked as “Solved”

---

## 📁 Recommended Folder Layout

Weak-Reset-Token-Abuse/
├── README.md
├── TODO.md
├── discovered_token.txt
└── screenshots/
├── token_found.png
├── reset_page.png
├── login_success.png
└── challenge_solved.png

yaml
Copy
Edit

---

✍️ Authored by: **SHajohn16** | 🧠 OffSec-Inspired Playbook | 🔓 Focus 

