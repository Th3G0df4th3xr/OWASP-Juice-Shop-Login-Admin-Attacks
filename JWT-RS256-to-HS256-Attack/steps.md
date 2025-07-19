# Steps for JWT-RS256-to-HS256-Attack
# ✅ TO-DO – JWT RS256 to HS256 Downgrade Attack

- [x] Start OWASP Juice Shop in Docker
- [x] Log in with a regular user account and capture JWT token from browser (localStorage or HTTP response)
- [x] Decode JWT using `jwt_tool.py` or `jwt.io`
- [x] Modify header to use `"alg": "HS256"`
- [x] Modify payload to impersonate:  
  `"email": "admin@juice-sh.op"`  
  `"role": "admin"`
- [x] Use `jwt_tool.py` to sign with public key as secret:
  ```bash
  jwt_tool.py -S HS256 -p 'admin@juice-sh.op' --secret ./public.pem
