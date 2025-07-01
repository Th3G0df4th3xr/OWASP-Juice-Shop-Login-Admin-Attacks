# ✅ TO-DO – Replay Token From Challenge Hint  
🎯 **Challenge**: Login Admin (OWASP Juice Shop)  
🛠️ **Technique**: JWT Replay via Scoreboard Hint  
🧠 **Category**: Token Injection / Client-Side Exploitation  

---

## 🚧 Environment Preparation

- [x] Launch OWASP Juice Shop at `http://localhost:3000`
- [x] Navigate to the **Scoreboard** (⭐ icon in the top nav)
- [x] Locate the **"Login Admin"** challenge
- [x] Carefully inspect challenge **hints or hidden links**
  - Look for embedded JWT tokens
  - Token may appear as:
    - A direct JWT string (starts with `eyJ`)
    - A URL with token embedded:
      ```
      http://localhost:3000/#/administration?token=eyJhbGciOi...
      ```
- [x] **Copy the token** for use in later steps

---

## 🔐 Replay the Token via Browser DevTools

- [x] Open browser **Developer Tools** (`F12`)
- [x] Go to **Application** tab → Local Storage → `http://localhost:3000`
- [x] Check for existing key named `token`  
  - If missing, create it manually
- [x] Overwrite or inject the token by executing in Console:
  ```js
  localStorage.setItem("token", "<REPLAYED_JWT_HERE>");
✅ Expected Output: No error in the console, and the token key appears under localStorage.

🧪 Verify Access and Challenge Success
 Refresh the application (F5)

 Manually navigate to:

bash
Copy
Edit
http://localhost:3000/#/administration
 Confirm visibility of the admin dashboard (admin impersonation successful)

 Open the Scoreboard

 Validate that the "Login Admin" challenge is now marked solved

📸 Documentation (Optional but Recommended)
 Screenshot of token discovered in scoreboard

 Screenshot of token injection in localStorage

 Screenshot of admin panel access

 Screenshot of scoreboard showing challenge completion

📂 Recommended Folder Structure
vbnet
Copy
Edit
Replay-Token-Hint/
├── README.md
├── TODO.md
├── token_from_hint.txt         # (Optional) Raw token string
└── screenshots/
    ├── scoreboard_hint.png
    ├── localstorage_injection.png
    ├── admin_access.png
    └── challenge_solved.png
✍️ Author: SHajohn16


