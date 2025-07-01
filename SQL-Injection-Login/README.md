
---

````markdown
# 🛡️ SQL Injection – Login Bypass  
🎯 Challenge: *Login Admin* (OWASP Juice Shop)  
💥 Exploit Category: Authentication Bypass via Input-Based SQLi  
✍️ Author: SHajohn16

---

## 🎯 Objective

Exploit a classic **SQL injection vulnerability** in the login form of OWASP Juice Shop to bypass authentication and gain access to the application as the **admin user**—without knowing valid credentials.

This exploit demonstrates how improper sanitization of SQL inputs in authentication logic leads to full compromise of access controls. Upon successful exploitation, the attacker is logged in as the first user (usually `admin@juice-sh.op`), solving the **"Login Admin"** challenge.

---

## 🧠 Attack Methodology

### 🔍 Exploitation Strategy

OWASP Juice Shop's login endpoint is vulnerable to **unsanitized SQL queries** being evaluated in the backend when user input is directly interpolated into the authentication SQL statement.

This allows attackers to inject logical conditions like:
```sql
' OR 1=1 --
````

which evaluates to `true`, effectively bypassing password validation entirely.

---

## 🪜 Step-by-Step Walkthrough

### 1️⃣ Navigate to Login Page

* Open `http://localhost:3000/#/login` in the browser.

---

### 2️⃣ Inject SQL Payloads

#### In the **email** field, enter:

```sql
' OR 1=1 --
```

#### In the **password** field:

* You may enter **any value**, or leave it blank.
* This value is ignored due to the SQL logic override.

🧠 **Why It Works:**

The backend likely performs SQL similar to:

```sql
SELECT * FROM Users WHERE email = '<input>' AND password = '<input>';
```

With injection:

```sql
SELECT * FROM Users WHERE email = '' OR 1=1 -- ' AND password = '';
```

The `--` comment bypasses the rest of the query, allowing unconditional login as the first matching record.

---

### 3️⃣ Submit the Form

* Click **"Log in"** or press `Enter`.

---

### 4️⃣ Observe the Outcome

✅ **Expected Behavior**:

* You are now logged in as `admin@juice-sh.op` or the first user in the DB.
* Admin-specific UI may be available (e.g., `/#/administration`)
* Open the **Scoreboard**, and the challenge **"Login Admin"** should now be marked **Solved**

---

## 🔧 Technologies & Vulnerable Stack

| Layer       | Detail                             |
| ----------- | ---------------------------------- |
| Frontend    | Angular login page form            |
| Backend     | Node.js + Express REST API         |
| Database    | SQLite with SQL query construction |
| Auth Method | Credential validation via raw SQL  |

---

## 🔍 Misconfigurations & Weaknesses Exploited

| Misconfiguration                  | Impact                                |
| --------------------------------- | ------------------------------------- |
| ❌ No input sanitization           | Direct SQL query injection            |
| ❌ No use of parameterized queries | Unsafe string concatenation           |
| ✅ First user is admin             | Login results in privilege escalation |

---

## ✅ Success Criteria

* Application grants access without valid credentials
* User interface shows logged in as `admin@juice-sh.op`
* "Login Admin" challenge on scoreboard is marked **Solved**

---

## 🛡️ Mitigation Techniques

| Control                                  | Recommendation                             |
| ---------------------------------------- | ------------------------------------------ |
| ✅ Use parameterized queries              | Prevent SQLi by separating logic from data |
| 🔐 Implement proper input validation     | Sanitize user input for special characters |
| 🔒 Implement account lockout on failures | Prevent brute-force and logic injection    |

---

## 💡 Notes for Red Teamers

This challenge emulates **real-world input validation failures**, commonly found in legacy systems or poorly secured login modules. It highlights the need for strict backend-side data handling and demonstrates how a simple logical injection can collapse the entire authentication process.

---

## 🧾 References

* [OWASP SQL Injection Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html)
* [Juice Shop GitHub](https://github.com/juice-shop/juice-shop)
* [PayloadsAllTheThings – SQL Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/SQL%20Injection/README.md)

---

```

