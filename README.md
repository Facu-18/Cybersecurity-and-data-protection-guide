

# Cybersecurity Awareness for Software Developers: A Digital Guide Course.

## 1. Introduction: What is Cybersecurity?
Cybersecurity is the practice of protecting systems, networks, and programs from digital attacks. In the context of software development, it involves building resilient code that ensures the **CIA Triad**: Confidentiality, Integrity, and Availability.

### Why is it important?
For a developer, security is crucial because:
*   It protects sensitive user data from unauthorized access.
*   It maintains the reputation and trust of the company.
*   It prevents financial losses due to **data breaches**.




## 2. Common Cyberattacks & Threats
Understanding how attacks work is the first step toward prevention.

### SQL Injection (SQLi)

<div align="center">
  <video src="https://github.com/user-attachments/assets/c241ceae-2dcf-4f7f-b824-ca0cba31f647" 
         width="500" 
         controls>
  </video>
</div>

#### Explanation
SQL Injection is a vulnerability that occurs when an application builds database queries by concatenating untrusted user input directly into the SQL statement. An attacker can inject malicious SQL fragments through input fields (like a login form or a search box) to alter the query's logic. This can let them bypass authentication, read data they shouldn't have access to, modify or delete records, and in severe cases take control of the entire database.

#### Example
A classic case is a login form that builds its query like this:

```sql
SELECT * FROM users WHERE username = 'INPUT' AND password = 'INPUT';
```

If an attacker enters `' OR '1'='1` as the username, the query becomes:

```sql
SELECT * FROM users WHERE username = '' OR '1'='1' AND password = '';
```

Since `'1'='1'` is always true, the condition passes and the attacker logs in without valid credentials. The video above demonstrates this attack in action.

#### How to Fix It
- **Use parameterized queries (prepared statements).** Never concatenate user input into SQL. Let the database driver handle the values separately from the query structure.
- **Use an ORM** such as Entity Framework, which parameterizes queries by default.
- **Validate and sanitize input**, applying a whitelist of allowed characters and formats where possible.
- **Apply the principle of least privilege** to the database account, so even a successful injection has limited reach.
- **Avoid exposing detailed database errors** to the end user.

A safe version in C# with Entity Framework / ADO.NET:

```csharp
// Vulnerable
var query = "SELECT * FROM Users WHERE Username = '" + input + "'";

// Safe — parameterized
var query = "SELECT * FROM Users WHERE Username = @username";
command.Parameters.AddWithValue("@username", input);
```

### Cross-Site Scripting (XSS):

-- Explicacion
-- Ejemplo video
-- Como solucionarlo

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



