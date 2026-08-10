# GitHub Actions + Docker

Typical pipeline:

```text
Git Push
   ↓
GitHub Actions
   ↓
Checkout
   ↓
Test
   ↓
Docker Build
   ↓
Security Scan
   ↓
Docker Push
   ↓
Registry
   ↓
Deployment
```

Use GitHub Actions secrets or identity federation for authentication. Never hard-code registry credentials.
