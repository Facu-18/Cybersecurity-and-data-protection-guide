

# Cybersecurity Awareness for Software Developers: A Digital Guide Course.

## 1. Introduction: What is Cybersecurity?
Cybersecurity is the practice of protecting systems, networks, and programs from digital attacks. In the context of software development, it involves building resilient code that ensures the **CIA Triad**: Confidentiality, Integrity, and Availability.

### Why is it important?
For a developer, security is crucial because:
*   It protects sensitive user data from unauthorized access.
*   It maintains the reputation and trust of the company.
*   It prevents financial losses due to **data breaches**.




## 2. Common Cyberattacks & Threats

> 🛡️ *Understanding how attacks work is the first step toward prevention.*

---

### 🔓 SQL Injection (SQLi)

![Severity](https://img.shields.io/badge/Severity-Critical-red?style=flat-square)
![OWASP](https://img.shields.io/badge/OWASP-A03:2021–Injection-orange?style=flat-square)
![Category](https://img.shields.io/badge/Type-Injection-yellow?style=flat-square)

<div align="center">
  <video src="https://github.com/user-attachments/assets/c241ceae-2dcf-4f7f-b824-ca0cba31f647" 
         width="500" 
         controls>
  </video>
</div>

<br>

#### 📖 Explanation

SQL Injection is a vulnerability that occurs when an application builds database queries by concatenating untrusted user input directly into the SQL statement. An attacker can inject malicious SQL fragments through input fields (like a login form or a search box) to alter the query's logic. This can let them **bypass authentication, read data they shouldn't have access to, modify or delete records**, and in severe cases take control of the entire database.

#### 🎯 Example

A classic case is a login form that builds its query like this:

```sql
SELECT * FROM users WHERE username = 'INPUT' AND password = 'INPUT';
```

If an attacker enters `' OR '1'='1` as the username, the query becomes:

```sql
SELECT * FROM users WHERE username = '' OR '1'='1' AND password = '';
```

Since `'1'='1'` is always true, the condition passes and the attacker logs in without valid credentials. 🎥 *The video above demonstrates this attack in action.*

#### 🛠️ How to Fix It

| ✅ Best Practice | Description |
|-----------------|-------------|
| **Parameterized queries** | Never concatenate user input into SQL. Let the driver handle values separately from the query structure. |
| **Use an ORM** | Tools like Entity Framework parameterize queries by default. |
| **Validate & sanitize input** | Apply a whitelist of allowed characters and formats. |
| **Least privilege** | Limit the database account's permissions so a successful injection has limited reach. |
| **Hide DB errors** | Never expose detailed database errors to the end user. |

<details>
<summary>💻 <b>Code Example (C#)</b></summary>

```csharp
// ❌ Vulnerable
var query = "SELECT * FROM Users WHERE Username = '" + input + "'";

// ✅ Safe — parameterized
var query = "SELECT * FROM Users WHERE Username = @username";
command.Parameters.AddWithValue("@username", input);
```

</details>

---

### 💉 Cross-Site Scripting (XSS)

![Severity](https://img.shields.io/badge/Severity-High-orange?style=flat-square)
![OWASP](https://img.shields.io/badge/OWASP-A03:2021–Injection-orange?style=flat-square)
![Category](https://img.shields.io/badge/Type-Client--Side_Injection-yellow?style=flat-square)

<div align="center">
  <video src="https://github.com/user-attachments/assets/a70f6e46-eec8-4fbc-9b96-4878fd5b2b4e" 
         width="500" 
         controls>
  </video>
</div>

<br>

#### 📖 Explanation

Cross-Site Scripting (XSS) is a vulnerability that occurs when an application includes untrusted user input in a web page **without properly escaping or sanitizing it**. This allows an attacker to inject malicious scripts (usually JavaScript) that run in the browser of other users who visit the page. Because the script appears to come from a trusted site, the browser executes it as legitimate code. A successful attack can let an attacker **steal session cookies, hijack user accounts, redirect victims to malicious sites, or deface the page**.

There are three main types:

| Type | How it works |
|------|--------------|
| **Reflected** | The malicious script is part of the request (e.g. a URL parameter) and is immediately reflected back in the response. |
| **Stored** | The script is saved on the server (e.g. in a comment or post) and runs for every user who views it. |
| **DOM-based** | The vulnerability lives in client-side JavaScript that handles user input unsafely, without the server being involved. |

#### 🎯 Example

Imagine a comment box that displays user input directly in the page:

```html
<p>Latest comment: USER_INPUT</p>
```

If an attacker submits the following as their comment:

```html
<script>alert('XSS')</script>
```

The browser renders it as executable code instead of plain text, and the script runs for everyone who views the page. A real attacker would replace the harmless `alert()` with code to steal cookies or session tokens. 🎥 *The video above demonstrates this attack in action.*

#### 🛠️ How to Fix It

| ✅ Best Practice | Description |
|-----------------|-------------|
| **Output encoding** | Escape user data based on context (HTML, attribute, JavaScript, URL) before rendering it. This is the primary defense. |
| **Input validation** | Apply a whitelist of allowed characters and reject unexpected input. |
| **Content Security Policy (CSP)** | Use a CSP header to restrict which scripts the browser is allowed to execute. |
| **Use safe framework features** | Modern frameworks auto-escape by default — avoid bypassing them (e.g. `innerHTML`, `@Html.Raw`). |
| **HttpOnly cookies** | Mark session cookies as `HttpOnly` so they can't be read by JavaScript. |

<details>
<summary>💻 <b>Code Example (ASP.NET / Razor)</b></summary>

```csharp
// ❌ Vulnerable — renders raw HTML, scripts will execute
@Html.Raw(userComment)

// ✅ Safe — Razor auto-encodes by default
@userComment
```

</details>

---


### Cross-Site Request Forgery (CSRF):

-- Explicacion
-- Ejemplo video
-- Como solucionarlo

## 3. Security Best Practices & Authentication

## 3. Security Best Practices & Authentication

> 🔐 *Writing secure code isn't optional — it's the baseline. This section covers the practices every developer must apply before shipping anything to production.*

---

### 3.1 Password Hashing — bcrypt & Argon2

When a user creates an account, **never store their password in plain text or in a reversibly encrypted form**. The correct approach is to store a *hash* of the password: a one-way transformation that can't be reversed. If your database is ever leaked, attackers won't get the real passwords.

But not all hashing algorithms are suitable for passwords. **MD5 and SHA-1 were designed to be fast** — which makes them dangerous for this use case. With modern hardware, an attacker can try billions of combinations per second (*brute force* or dictionary attacks).

Algorithms like **bcrypt** and **Argon2** are deliberately slow and have a configurable cost factor, making mass attacks impractical.

#### How bcrypt works

- Automatically generates a random **salt** per password, so two users with the same password produce completely different hashes.
- The **work factor** controls how many iterations the algorithm runs. A value of `12` is a solid default today.

```javascript
// Node.js — bcryptjs
const bcrypt = require('bcryptjs');

// On user registration
const hashedPassword = await bcrypt.hash(plainTextPassword, 12);
// ✅ Store hashedPassword in the DB — NEVER plainTextPassword

// On login
const isValid = await bcrypt.compare(inputPassword, storedHash);
if (!isValid) return res.status(401).json({ message: 'Invalid credentials' });
```

```python
# Python — bcrypt
import bcrypt

# On registration
hashed = bcrypt.hashpw(b"my_password", bcrypt.gensalt(rounds=12))

# On login
if bcrypt.checkpw(input_password.encode(), hashed):
    print("Access granted")
else:
    print("Invalid credentials")
```

#### Why Argon2 is even better

**Argon2** won the Password Hashing Competition in 2015. Unlike bcrypt, it's also resistant to GPU and FPGA-accelerated attacks because it's memory-hard. For new projects, prefer Argon2 over bcrypt.

```python
# Python — argon2-cffi
from argon2 import PasswordHasher

ph = PasswordHasher(time_cost=3, memory_cost=65536, parallelism=1)

# On registration
hash = ph.hash("my_password")

# On login
try:
    ph.verify(hash, "my_password")
    print("Valid")
except Exception:
    print("Invalid password")
```

> ❌ **Never use:** `md5(password)`, `sha1(password)`, or bare `sha256(password)`.
> They have no salt by default and are trivially broken with rainbow tables or dictionary attacks.

---

### 3.2 Never Hardcode Credentials — Environment Variables & `.env`

One of the most common (and most damaging) mistakes is writing credentials directly in source code:

```javascript
// ❌ NEVER do this
const db = mysql.createConnection({
  host: 'localhost',
  user: 'root',
  password: 'SuperSecret123!',
  database: 'production'
});

const apiKey = "sk-live-abc123realsecret";
```

If this code ever reaches a public GitHub repo — or even a private one that gets compromised — the credentials are exposed. There are bots that scan GitHub in real time specifically hunting for patterns like these.

#### The fix: environment variables

Credentials belong in the runtime environment, not in source code. For local development, the standard is a `.env` file:

```bash
# .env  ← NEVER commit this file
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=SuperSecret123!
DB_NAME=production
API_KEY=sk-live-abc123realsecret
JWT_SECRET=a_long_random_string_goes_here
```

Read those values in your code at runtime:

```javascript
// Node.js — dotenv
require('dotenv').config();

const db = mysql.createConnection({
  host: process.env.DB_HOST,
  user: process.env.DB_USER,
  password: process.env.DB_PASSWORD,
  database: process.env.DB_NAME
});
```

```python
# Python — python-dotenv
from dotenv import load_dotenv
import os

load_dotenv()
api_key = os.getenv("API_KEY")
```

#### Always add `.env` to `.gitignore`

```gitignore
# .gitignore
.env
.env.local
.env.production
*.env
```

#### Share variable names, not values — `.env.example`

Commit a `.env.example` file with all the variable names but empty values. This tells your teammates what variables the project needs without leaking the secrets:

```bash
# .env.example  ← Commit this one ✅
DB_HOST=
DB_USER=
DB_PASSWORD=
API_KEY=
JWT_SECRET=
```

> ⚠️ **If you accidentally commit a secret**, deleting it from the file is not enough — it lives in Git history forever. **Rotate the credential immediately** (generate a new one and revoke the old one), then optionally clean the history.

---

### 3.3 Secrets Management in Production

`.env` files work fine locally, but in production environments — especially with multiple services or teams — you need something more robust and auditable.

**Secrets management tools** centralize credentials, handle automatic rotation, enforce access control per service, and keep an audit log of every access.

#### HashiCorp Vault (self-hosted / multi-cloud)

```bash
# Store a secret
vault kv put secret/my-app db_password="SuperSecret123!"

# Read it at runtime
vault kv get -field=db_password secret/my-app
```

```javascript
// Node.js — node-vault
const vault = require('node-vault')({ token: process.env.VAULT_TOKEN });

const { data } = await vault.read('secret/data/my-app');
const dbPassword = data.data.db_password;
// The app fetches the secret at startup — never hardcoded
```

#### AWS Secrets Manager (cloud-native)

```javascript
const { SecretsManagerClient, GetSecretValueCommand } = require("@aws-sdk/client-secrets-manager");

const client = new SecretsManagerClient({ region: "us-east-1" });
const response = await client.send(
  new GetSecretValueCommand({ SecretId: "my-app/db" })
);
const secret = JSON.parse(response.SecretString);
// secret.DB_PASSWORD, secret.API_KEY, etc.
```

| Tool | Best for |
|------|----------|
| **HashiCorp Vault** | On-premise, multi-cloud, fine-grained control |
| **AWS Secrets Manager** | AWS-native workloads |
| **Azure Key Vault** | Azure workloads |
| **GCP Secret Manager** | GCP workloads |

> 💡 **Rule of thumb:** if more than one service or person needs a secret, it belongs in a secrets manager — not in a `.env` file on a shared server.

---

### 3.4 HTTPS / TLS — Why It Matters

HTTP sends everything in plain text. Anyone on the same network — a coffee shop, a corporate network, a malicious ISP — can intercept the traffic and read passwords, tokens, and sensitive data with a basic packet sniffer like Wireshark. This is called a **man-in-the-middle (MITM) attack**.

**HTTPS** (HTTP over TLS) encrypts the communication between client and server. Even if traffic is intercepted, the content is unreadable.

#### What this means for you as a developer

**1. Never accept credentials or tokens over plain HTTP.**
A login endpoint over HTTP is a vulnerability. Full stop.

**2. Redirect HTTP → HTTPS automatically:**

```nginx
# Nginx
server {
    listen 80;
    server_name myapp.com;
    return 301 https://$host$request_uri;
}
```

**3. Add HSTS** (HTTP Strict Transport Security) — tells the browser to *always* use HTTPS, even if the user types `http://`:

```javascript
// Express.js — helmet
const helmet = require('helmet');

app.use(helmet.hsts({
  maxAge: 31536000,       // 1 year in seconds
  includeSubDomains: true,
  preload: true
}));
```

**4. Never disable TLS certificate verification in outbound requests:**

```python
# ❌ Insecure — skips certificate verification
requests.get("https://external-api.com/data", verify=False)

# ✅ Correct — verifies the certificate (default behavior)
requests.get("https://external-api.com/data")
```

> 💡 For local HTTPS development, use [mkcert](https://github.com/FiloSottile/mkcert) to generate valid certificates for `localhost` without browser warnings.

---

### 3.5 JWT and OAuth 2.0 — Core Concepts

#### JWT (JSON Web Token)

A JWT is a compact, self-contained token that carries user information (called *claims*). It has three Base64-encoded parts separated by dots: `header.payload.signature`.

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.        ← Header (algorithm)
eyJ1c2VySWQiOiIxMjMiLCJyb2xlIjoiYWRtaW4ifQ.  ← Payload (your data)
SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c  ← Signature (tamper-proof)
```

The server signs the token on login. On every subsequent request, the client sends the token and the server verifies it — **no database lookup needed**.

```javascript
// Node.js — jsonwebtoken
const jwt = require('jsonwebtoken');

// On successful login → generate token
const token = jwt.sign(
  { userId: user.id, role: user.role },  // payload
  process.env.JWT_SECRET,                // secret (keep this safe!)
  { expiresIn: '1h' }                    // always set an expiration
);
res.json({ token });

// Middleware to protect routes
function authenticate(req, res, next) {
  const authHeader = req.headers['authorization'];
  const token = authHeader?.split(' ')[1]; // "Bearer <token>"

  if (!token) return res.status(401).json({ message: 'Token required' });

  try {
    req.user = jwt.verify(token, process.env.JWT_SECRET);
    next();
  } catch {
    return res.status(403).json({ message: 'Invalid or expired token' });
  }
}

// Usage
app.get('/protected', authenticate, (req, res) => {
  res.json({ message: `Hello, user ${req.user.userId}` });
});
```

#### Critical rules for JWTs

| ✅ Do | ❌ Don't |
|-------|---------|
| Always set `expiresIn` | Create tokens with no expiration |
| Use a long random `JWT_SECRET` (256+ bits) | Use a short or predictable secret |
| Store only non-sensitive data in the payload | Store passwords or PII in the payload |
| Use `HttpOnly` + `Secure` cookies for web apps | Store tokens in `localStorage` (XSS risk) |

Generate a strong secret:
```bash
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
```

> ⚠️ The JWT payload is **Base64-encoded, not encrypted**. Anyone can decode it. Never store sensitive information there (passwords, credit cards, etc.).

---

#### OAuth 2.0

OAuth 2.0 is an **authorization protocol** that lets your app access a user's resources on another service — like Google or GitHub — **without the user giving you their password**. It's what powers "Sign in with Google".

The most common flow for web apps is the **Authorization Code Flow**:

```
1. Your app  →  Redirect user to Google's login page
2. Google    →  User authenticates and approves permissions
3. Google    →  Redirects back to your app with a temporary `code`
4. Your app  →  Exchanges that `code` for an access_token (server-side)
5. Your app  →  Uses access_token to call Google APIs on behalf of the user
```

```javascript
// Node.js — passport-google-oauth20
const GoogleStrategy = require('passport-google-oauth20').Strategy;

passport.use(new GoogleStrategy({
  clientID:     process.env.GOOGLE_CLIENT_ID,
  clientSecret: process.env.GOOGLE_CLIENT_SECRET,
  callbackURL:  '/auth/google/callback'
}, async (accessToken, refreshToken, profile, done) => {
  // Find or create the user in your DB using profile.id
  const user = await User.findOrCreate({ googleId: profile.id });
  return done(null, user);
}));

// Routes
app.get('/auth/google',
  passport.authenticate('google', { scope: ['profile', 'email'] })
);

app.get('/auth/google/callback',
  passport.authenticate('google', { failureRedirect: '/login' }),
  (req, res) => res.redirect('/dashboard')
);
```

#### JWT vs OAuth 2.0 — Quick comparison

| | JWT | OAuth 2.0 |
|---|---|---|
| **What it is** | A token format | An authorization protocol |
| **Used for** | Stateless authentication between client and your API | Delegating access to third-party services |
| **Who issues it** | Your own server | A third-party provider (Google, GitHub…) |
| **Typical use case** | "Is this user logged in to *my* app?" | "Let my app access this user's Google Drive" |

> 💡 When OAuth 2.0 is combined with **OpenID Connect (OIDC)**, it also handles authentication — this is exactly what "Sign in with Google" does under the hood.

---

### ✅ Section Summary

| Practice | Why it matters |
|----------|---------------|
| Use **bcrypt** or **Argon2** for passwords | Makes brute-force attacks computationally infeasible |
| **Never hardcode** credentials in source code | Prevents accidental exposure via version control |
| Use **`.env`** locally, secrets manager in production | Separates config from code; enables secret rotation |
| Enforce **HTTPS everywhere** | Prevents MITM attacks and credential interception |
| Use **JWT** with expiration and a strong secret | Enables stateless auth without per-request DB lookups |
| Use **OAuth 2.0** for third-party integrations | Delegates access safely without handling third-party passwords |

Hashing de contraseñas (bcrypt, Argon2 — nunca MD5/SHA1 plano).
No hardcodear credenciales; uso de variables de entorno y .env fuera del repo.
Secrets management (Vault, AWS Secrets Manager) y por qué no commitear claves.
HTTPS/TLS y por qué importa.
Conceptos básicos de JWT y OAuth2.



## 6. Bibliography & Resources

Inyección SQL: El Hackeo Más Sencillo y Peligroso que Existe
https://youtu.be/tdtAmH3ZSAI?si=5nL_qpCOjrgrFD7a
By Migma



