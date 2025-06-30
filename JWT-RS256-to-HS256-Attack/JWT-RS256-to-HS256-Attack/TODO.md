Certainly. Below is a full **`TODO.md` file** tailored for the **JWT RS256 to HS256 algorithm confusion attack** used to solve the **"Login Admin" challenge in OWASP Juice Shop**. It’s designed in an **OffSec-style**, including deep-dive references, task breakdowns, and direct linkage to penetration testing methodology.

---

# ✅ TODO – JWT RS256 to HS256 Attack

### 🎯 Target: OWASP Juice Shop – *"Login Admin"* Challenge

**Author:** SHajohn16
**Focus:** Authentication Bypass via JWT Signature Forgery
**TTPs:** Token tampering, cryptographic abuse, server-side misconfiguration

---

## 🚧 Task List

### 🛠️ Exploit Development

* [ ] 🔧 **Add exploit script**:

  * Create `forge_token.py` to sign a malicious HS256 JWT using Juice Shop’s RSA public key as the HMAC key.
  * Ensure payload sets `email: admin@juice-sh.op` to trigger privilege escalation.
  * Validate output using jwt.io/debugger.

* [ ] 🧪 **Write token replay instructions**:

  * Document how to replace the token manually in Burp Suite.
  * Add instructions for injecting it via browser DevTools → localStorage or cookies.

---

### 📸 Visual Proof

* [ ] 📷 **Add Burp Suite screenshots**:

  * Show intercepted JWT token with `alg: RS256`.
  * Show modified request with forged HS256 token.
  * Show admin access (e.g., “Login Admin” challenge marked as solved on scoreboard).

---

### 📚 Technical Write-up

* [ ] 📄 **Document mitigation strategies**:

  * Enforce algorithm verification on server side.
  * Block `alg` override from token header.
  * Avoid exposing `.well-known/jwks.json` or implement access controls.

* [ ] ⚠️ **Reference related CVEs and writeups**:

  * [CVE-2015-9235](https://nvd.nist.gov/vuln/detail/CVE-2015-9235) – Auth0 JWT RS256 to HS256 vulnerability.
  * [Auth0 blog](https://auth0.com/blog/critical-vulnerabilities-in-json-web-token-libraries/) – Explains widespread library flaws.
  * OWASP JWT Cheat Sheet – Secure implementation tips.

---

## 📋 Summary

This `TODO.md` serves as a tactical checklist for demonstrating an **advanced authentication bypass** in a real-world vulnerable application. The exploitation uses algorithm confusion to convert RS256 to HS256, crafting a token with elevated privileges using only the publicly available RSA key—violating core cryptographic principles.

It reflects:

* **Adversarial Tactics** from JWT fuzzing to payload injection.
* **OffSec Red Team Techniques** for signature forgery.
* **Real CVE TTP Reuse** on a controlled testbed (Juice Shop).

---

## 📂 Final Structure After Completion

```
JWT-RS256-to-HS256-Attack-JuiceShop/
├── 01_get_jwks.py
├── 02_jwks_to_pem.py
├── 03_forge_token.py
├── screenshots/
│   ├── intercepted_jwt.png
│   ├── modified_request.png
│   └── admin_logged_in.png
├── forged_token.txt
├── public.pem
├── README.md
├── TODO.md
└── references.md
```

---

