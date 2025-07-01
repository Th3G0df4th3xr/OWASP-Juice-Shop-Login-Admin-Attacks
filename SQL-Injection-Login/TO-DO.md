 **OffSec-style `TODO.md`** for the **SQL Injection – Login Bypass** method used to solve the **"Login Admin"** challenge in OWASP Juice Shop.

---

````markdown
# ✅ TO-DO – SQL Injection Login Bypass  
🎯 Challenge: *Login Admin*  
🛠️ Technique: Classic Authentication Bypass using SQLi  
✍️ Author: SHajohn16  

---

## 🧪 Pre-Exploit Preparation

- [x] Launch OWASP Juice Shop locally at `http://localhost:3000`
- [x] Navigate to the login page → `/#/login`
- [x] Open browser DevTools (optional) to monitor requests/responses

---

## 💉 Exploit Execution – SQL Injection

- [x] In the **email/username** field, input the payload:
  ```sql
  ' OR 1=1 --
````

* [x] In the **password** field:

  * Leave it empty, or enter any dummy text (e.g., `test`)

* [x] Submit the login form by clicking **Log In**

✅ **Expected Behavior**:

* Application logs you in as the **first user** in the database (usually `admin@juice-sh.op`)
* No credential validation is performed due to the always-true SQL logic
* Admin-related UI elements become visible

---

## 🎯 Post-Exploitation Validation

* [x] Navigate to the `/administration` route:

  ```
  http://localhost:3000/#/administration
  ```

* [x] Open the **Scoreboard** from the top navbar

* [x] Confirm the challenge **“Login Admin”** is now marked as **Solved ✅**

---

## 📸 Documentation & Evidence Collection

* [ ] Screenshot of the login input with SQLi payload
* [ ] Screenshot showing the logged-in admin interface
* [ ] Screenshot of the scoreboard showing the challenge completed

---

## 📂 Suggested Folder Structure

```
SQL-Injection-Login-Bypass/
├── README.md
├── TODO.md
├── screenshots/
│   ├── sqli_payload_login.png
│   ├── admin_dashboard.png
│   └── challenge_solved.png
```

---

✍️ Authored by: **SHajohn16** | 🧠 OffSec-inspired Methodology

```

