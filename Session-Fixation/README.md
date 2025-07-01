This is a deep-dive, fully elaborated, professional OffSec-style version of the README.md for the Session Fixation via Unprotected JWT attack in OWASP Juice Shop.

It includes:

🎯 Objective

⚙️ Underlying tech & stack

🧠 Full attack flow

🪜 Step-by-step execution

🔬 Analysis of flaws exploited

✅ Expected outcome

🛡️ Mitigation strategies

markdown
Copy
Edit
# 🔐 Session Fixation via Unprotected JWT  
🎯 Challenge Type: Authentication Token Abuse  
🧠 Category: Web Session Exploitation / Insecure Authentication  
✍️ Author: SHajohn16

---

## 🎯 Objective

This attack demonstrates a **session fixation vulnerability** in OWASP Juice Shop by exploiting the **lack of JWT rotation** during login. The attacker crafts or steals a JSON Web Token (JWT), delivers it to the victim (via social engineering or injection vectors), and once the victim logs in using that token, the attacker is granted **parallel access to the same session**.

This vulnerability reflects real-world flaws where applications fail to invalidate or rotate session tokens, allowing attackers to predefine or lock-in a session for unauthorized reuse.

---

## ⚙️ Technologies & Stack Behind the Exploit

| Component           | Description                                        |
|---------------------|----------------------------------------------------|
| 🧩 JWT               | JSON Web Token used for stateless authentication   |
| 🌐 Frontend          | Angular Single Page Application                    |
| 🔙 Backend           | Node.js + Express REST API                         |
| 📁 Storage Layer     | JWT stored in `localStorage`                       |
| 🔐 Auth Behavior     | JWT not invalidated or rotated after login         |
| 🧪 Testing Tools     | Burp Suite / Browser DevTools                      |

---

## 🧠 Attack Methodology Overview

This technique targets a **failure in token lifecycle management**:

1. JWTs are generated and persisted client-side using `localStorage`.
2. Tokens are **not rotated** or **invalidated** upon authentication events.
3. Attacker provides a JWT token (either self-issued or obtained via recon).
4. Victim uses the injected token to authenticate.
5. Session remains “fixed” and shared across both attacker and victim.

This results in a successful **session fixation**, enabling the attacker to hijack any user who logs in using the pre-shared token.

---

## 🪜 Step-by-Step Attack Walkthrough

### 1️⃣ Log in with Your (Attacker) Account

- Navigate to: `http://localhost:3000/#/login`
- Enter credentials for any user (e.g., `test@juice-sh.op`)
- Open browser **Developer Tools** → `Application` tab → `Local Storage`
- Locate the JWT key under:  
Local Storage → http://localhost:3000 → token

yaml
Copy
Edit

✅ **Expected Output:** You should see a valid JWT string starting with `eyJ...`

---

### 2️⃣ Capture and Store the Token

- Copy the full token value and store it in a local text file (e.g., `token.txt`)
- Optionally decode it using [https://jwt.io](https://jwt.io) to confirm structure:
```json
{
  "email": "test@juice-sh.op",
  "role": "customer"
}
3️⃣ Deliver Token to the Victim
Use one of the following approaches:

🟠 Phishing Link: Create a link like:

bash
Copy
Edit
http://localhost:3000/#/login?token=<your_token>
🔴 XSS Injection (if available): Inject a script that sets the token:

js
Copy
Edit
localStorage.setItem('token', '<ATTACKER_TOKEN>')
🧠 The idea is to force the victim’s browser to store the attacker’s JWT in their localStorage.

4️⃣ Victim Logs In with the Injected Token
Victim clicks the link or the injected script runs

The browser stores the JWT before or during login

Victim’s session is now locked to the attacker-defined token

✅ Expected Outcome:

Victim sees themselves logged in normally

Since the JWT is fixed and shared, the attacker can reuse the same token at any time and access the victim’s session in parallel

5️⃣ Replay the Session as Attacker
Attacker opens Juice Shop

Injects the same JWT back into their localStorage:

js
Copy
Edit
localStorage.setItem('token', '<REUSED_JWT>')
window.location.reload()
Navigate to /#/profile or /#/administration (if victim has admin rights)

✅ Session successfully hijacked. Challenge solved if session led to elevated access.

🔍 Misconfigurations Exploited
Vulnerability	Impact
❌ No JWT rotation	Allows reuse of static token across sessions
❌ Stateless auth only	No server-side session tracking or invalidation
❌ No binding to context	Tokens are not tied to IP, UA, or session fingerprint
❌ No exp claim enforced	Long-lived or manually crafted tokens remain valid indefinitely

✅ Indicators of Success
Checkpoint	Expected Outcome
Token injection into localStorage	No errors; token key is set
Victim uses attacker-supplied JWT	Logs in without resistance
Attacker reuses same token	Gains identical access as victim
Scoreboard update	Challenge marked as Solved ✅

🔐 Mitigation Strategies
Control	Description
✅ Rotate tokens post-login	Always issue new JWT after authentication
✅ Enforce short TTL (exp)	Time-limit token lifetime
✅ Bind JWT to fingerprint	Tie to IP, User-Agent, or session ID
✅ Implement revocation list	Track JWTs on server to manually revoke access
✅ Use HttpOnly secure cookies	Avoid storing tokens in localStorage

💡 Real-World Implications
Session fixation attacks using JWTs are high-impact due to the stateless nature of token-based authentication. When token lifecycle is not handled securely, it enables pre-auth control, parallel access, and persistent session hijacking. This vulnerability is particularly dangerous in SPAs and REST APIs that trust client-side storage without revalidation or rotation.

🧾 References
OWASP Cheat Sheet – Session Fixation

JWT Best Practices – Auth0

RFC 7519 – JSON Web Tokens (JWT)

