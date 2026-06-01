

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

Hashing de contraseñas (bcrypt, Argon2 — nunca MD5/SHA1 plano).
No hardcodear credenciales; uso de variables de entorno y .env fuera del repo.
Secrets management (Vault, AWS Secrets Manager) y por qué no commitear claves.
HTTPS/TLS y por qué importa.
Conceptos básicos de JWT y OAuth2.



## 6. Bibliography & Resources

Inyección SQL: El Hackeo Más Sencillo y Peligroso que Existe
https://youtu.be/tdtAmH3ZSAI?si=5nL_qpCOjrgrFD7a
By Migma



