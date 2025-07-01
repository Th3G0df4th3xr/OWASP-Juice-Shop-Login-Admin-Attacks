OffSec-style for the Session Fixation via Unprotected JWT attack in OWASP Juice Shop:

markdown
Copy
Edit
# ✅ TO-DO – Session Fixation via Unprotected JWT  
🎯 Challenge: *Login Admin* or *Session Fixation*  
🛠️ Technique: JWT Reuse Without Rotation  
✍️ Author: SHajohn16  

---

## 🧪 Phase 1 – Setup & Token Acquisition

- [x] Launch OWASP Juice Shop at `http://localhost:3000`
- [x] Register and log in as a low-privileged user (e.g., `attacker@juice-sh.op`)
- [x] Open **Developer Tools → Application → Local Storage**
- [x] Locate the JWT stored under the `token` key
- [x] Copy and save the JWT to a file (e.g., `attacker_token.txt`)

✅ *Expected Outcome:* A valid JWT is captured and ready for reuse.

---

## 🎯 Phase 2 – Deliver the Token to the Victim (Simulated)

- [x] Simulate delivering the token to the victim using either method:
  - [ ] A crafted phishing link:
    ```
    http://localhost:3000/#/login?token=<ATTACKER_TOKEN>
    ```
  - OR
  - [ ] Manually inject the token in the victim's browser console:
    ```js
    localStorage.setItem("token", "<ATTACKER_TOKEN>");
    ```

✅ *Expected Outcome:* The victim’s browser now stores the attacker’s JWT.

---

## 🔓 Phase 3 – Victim Logs In

- [x] Victim logs in or refreshes the browser
- [x] Application authenticates using the attacker-controlled token

✅ *Expected Outcome:* The victim unknowingly shares the same session as the attacker.

---

## 🔁 Phase 4 – Attacker Reuses the Session

- [x] Attacker reinjects the same JWT token into their own browser:
  ```js
  localStorage.setItem("token", "<ATTACKER_TOKEN>");
  window.location.reload();
 Navigate to a sensitive route, such as:

bash
Copy
Edit
http://localhost:3000/#/administration
✅ Expected Outcome: Attacker successfully accesses victim’s session or privileges.

📸 Phase 5 – Documentation & Reporting
 Screenshot of JWT token in localStorage (attacker side)

 Screenshot of the victim browser after injection

 Screenshot of admin panel access by attacker

 Screenshot of the challenge marked as “Solved” in scoreboard

📂 Recommended Folder Structure
markdown
Copy
Edit
Session-Fixation-JWT/
├── README.md
├── TODO.md
├── attacker_token.txt
└── screenshots/
    ├── token_exfiltrated.png
    ├── victim_browser_injection.png
    ├── attacker_access.png
    └── challenge_solved.png
✍️ Authored by: SHajohn16 | 🧠 OffSec-Inspired | 💉 Focus Area: Insecure Session Management

