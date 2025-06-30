# 🔐 OWASP Juice Shop - Login Admin Exploit Collection

This repository documents multiple attack methodologies used to compromise the admin account in OWASP Juice Shop.

Each method contains:
- 🧪 Attack technique explanation
- 🧭 Step-by-step instructions
- 📸 Screenshots
- ✅ Proof of successful admin login

## 📁 Directory Structure

Each folder represents a distinct attack technique:
- `JWT-Token-Manipulation`
- `JWT-None-Algorithm-Exploit`
- `JWT-RS256-to-HS256-Attack`
- `Brute-Force-Admin-Credentials`
- `SQL-Injection-Login`
- `BurpSuite-Response-Manipulation`
- `Session-Fixation`
- `Weak-Reset-Token-Abuse`
- `Exposed-Admin-API`
- `LocalStorage-Tampering`
- `Replay-Token-From-Hint`
- `Broken-Access-Control`

## 📸 Common Proof of Concept
All attacks use the same final POC located at:
- `POC/OWASP JUICE SHOP - LOGIN ADMIN.png`

## ⚠️ JWT-Specific POC
JWT Token attack has its own separate visual proof:
- `POC/OWASP JUICE SHOP - LOGIN ADMIN via JWT TOKEN.png`

## 📚 Usage
Feel free to explore and reproduce each attack as part of your OSCP/OSWE learning.

---

