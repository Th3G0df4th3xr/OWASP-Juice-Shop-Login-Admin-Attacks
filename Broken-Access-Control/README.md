# 🔓 Attack: Broken Access Control – Admin Panel Access without Authorization

## 🎯 Objective
Exploit improper access control to access restricted admin functionalities without being authenticated as an admin user.

---

## 🧠 Background Knowledge
- **Access Control**: Restriction of access to parts of an application based on user role.
- **Broken Access Control**: A vulnerability where unauthorized users can perform privileged actions or access restricted data.
- **Common Causes**:
  - Role-based checks performed only on the frontend
  - Sensitive endpoints exposed and accessible without verification

---

## 🛠️ Technologies Involved
- OWASP Juice Shop (Node.js backend)
- Browser DevTools or Burp Suite
- LocalStorage / Cookies / HTTP headers

---

## 🧩 MITRE ATT&CK Mapping
- **T1078** – Valid Accounts (Bypass via direct URL access)
- **T1068** – Exploitation for Privilege Escalation

---

## 🧪 Step-by-Step Execution

### 🔹 1. Start Juice Shop Container
```bash
docker run -d -p 3000:3000 bkimminich/juice-shop
