Absolutely! Here's your updated **`TODO.md`** file in a **fully structured, professional, OffSec-style format**, extending your current checklist for the **Local Storage Tampering – JWT Overwrite** method, used to solve the **"Login Admin"** challenge in OWASP Juice Shop.

---

# ✅ TO-DO – Local Storage Tampering (JWT Overwrite Exploit)

🎯 **Challenge:** OWASP Juice Shop – *Login Admin*
🔐 **Technique:** Client-Side JWT Injection via `localStorage` Tampering
👤 **Target User:** `admin@juice-sh.op`

---

## 🚧 Pre-Attack Setup

* [x] **Launch OWASP Juice Shop** locally (`http://localhost:3000`)
* [x] **Log in using a non-privileged user account** (e.g., `test@juice-sh.op` / `test123`)
* [x] **Open Browser Developer Tools**

  * Navigate to `Application` tab → `Local Storage` → `http://localhost:3000`

---

## 🧠 JWT Discovery & Analysis

* [x] **Locate the JWT token** stored under the `token` key
* [x] **Copy and decode the token** using [https://jwt.io](https://jwt.io)

  * Review claims such as:

    ```json
    {
      "email": "test@juice-sh.op",
      "role": "customer"
    }
    ```

---

## 🔧 Token Crafting & Injection

* [x] **Create a forged token** with elevated privileges:

  ```json
  {
    "email": "admin@juice-sh.op",
    "role": "admin"
  }
  ```
* [x] **Generate a valid JWT** (use signature from previous attack or disable verification locally)
* [x] **Inject the token in browser console**:

  ```js
  localStorage.setItem("token", "<FORGED_JWT_TOKEN>");
  ```

---

## 🛠️ Access Validation & Challenge Completion

* [x] **Refresh the page**
* [x] **Visit:** `http://localhost:3000/#/administration`
* [x] **Verify admin dashboard access**
* [x] **Open scoreboard and confirm** the *"Login Admin"* challenge is marked **solved**

---

## 📸 Documentation & Reporting

* [ ] **Capture screenshots**:

  * [ ] `localStorage` with original and forged JWT
  * [ ] Access to admin panel
  * [ ] Scoreboard showing completed challenge

* [ ] **Push all contents to GitHub** under the folder:

  ```
  Local-Storage-Tampering/
  ├── README.md
  ├── TODO.md
  ├── screenshots/
  └── token_forged.txt (optional)
  ```

---

