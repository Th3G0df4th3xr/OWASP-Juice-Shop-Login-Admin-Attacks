Absolutely — here’s your **`TODO.md`** file for the `Exposed-Admin-API/` folder, designed in the same **OffSec-style structured format** as the previous challenge. This checklist guides the execution and documentation of the unauthenticated admin API abuse for the OWASP Juice Shop "Login Admin" challenge.

---

# ✅ TODO – Exposed Admin API Exploitation

### 🎯 Challenge: OWASP Juice Shop – *Login Admin*

**Author:** SHajohn16
**Technique:** Unauthenticated Access to REST Admin Endpoint
**Objective:** Obtain an admin JWT token by directly calling `/rest/user/login` and solve the challenge by bypassing the frontend.

---

## 🚧 Task List

---

### 🛠️ Exploit Execution

* [ ] 📡 **Send login request to admin API endpoint**

  * Use `curl` or `httpie` to call `POST /rest/user/login` with JSON payload.
  * Target: `admin@juice-sh.op` and known default password `admin123`.

* [ ] 🪙 **Extract and store the JWT token**

  * Parse the JSON response and extract the `token` field.
  * Save it to `token.txt` for use in injection or testing.

---

### 🚀 Access Injection

* [ ] 💻 **Inject token into LocalStorage (browser method)**

  * Use DevTools > Application > LocalStorage
  * Set: `localStorage.token = "<admin_token>"`

* [ ] 🧰 **Configure Burp Suite to replay the token (manual header injection)**

  * Intercept a request and add:

    ```
    Authorization: Bearer <admin_token>
    ```

* [ ] 🔁 **Refresh the app and validate admin access**

  * Navigate to account section → Verify you're logged in as `admin@juice-sh.op`.
  * Scoreboard should update to mark **"Login Admin"** as solved ✅.

---

### 📸 Visual Documentation

* [ ] 📷 **Capture Burp Suite request showing direct API login**

  * Include full POST request to `/rest/user/login`
  * Highlight lack of access control

* [ ] 🎯 **Screenshot of Scoreboard with "Login Admin" solved**

* [ ] 💻 **Screenshot of LocalStorage or JWT injection proof**

---

### 📚 Technical Write-up

* [ ] 📝 **Write README.md**

  * Include background, tech stack, step-by-step flow, command outputs, expectations, and validation.

* [ ] 🧠 **Document why the attack works**

  * Explain how unauthenticated APIs + token trust enables privilege escalation.

* [ ] 🔐 **List security misconfigurations exploited**

  * Insecure direct object access (IDOR) on auth endpoint
  * Missing authentication on protected route
  * No CSRF, Origin, or rate limiting validation

---

### 📖 References & Research

* [ ] 🔗 Link to Juice Shop GitHub and OWASP docs
* [ ] 🔗 Include any REST security misconfiguration references or OWASP API Top 10
* [ ] 🔗 Add blog references about abusing unauthenticated endpoints for token theft

---

## 📋 Expected Repo Outcome

```
Exposed-Admin-API/
├── curl_login_as_admin.sh         # Automates admin login and token parsing
├── token.txt                      # Output JWT used to solve the challenge
├── screenshots/
│   ├── api_login_burp.png
│   ├── jwt_injection.png
│   └── login_admin_solved.png
├── README.md                      # Detailed walkthrough
├── TODO.md                        # This tactical checklist
└── extended_description.txt       # Short abstract of exploit
```

---

