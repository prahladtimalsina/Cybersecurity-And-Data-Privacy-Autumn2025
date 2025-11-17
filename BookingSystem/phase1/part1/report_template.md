# 🛡️ **Penetration Test Report – Booking System (Phase 1)**

---

# 1️⃣ **Introduction**

### **Tester(s):**

**Name:** **Prahlad Timalsina**

### **Purpose:**

The purpose of this penetration test is to evaluate the security of the Booking System’s **registration and authentication flows**, deployed through the official Docker Compose file provided by the course.
The test aims to identify vulnerabilities related to input validation issues, injection risks, CSRF exposure, and missing security headers.

---

### **Scope:**

**Tested components:**

* `/register` page
* Client-side input validation
* HTTP request/response behavior
* Cookies & session handling
* Automated scanning using OWASP ZAP

**Exclusions:**

* Source code review (not provided)
* Backend database inspection
* Performance testing

### **Test approach:**

* **Black-box testing** – only the running Docker application was tested.

### **Test environment & dates:**

* **Start:** 15 Nov 2025
* **End:** 16 Nov 2025

**Environment details:**

* OS: **Arch Linux**
* Application runtime: **Docker Compose** (official course image)
* Browser: **Firefox & Chrome**
* Database: Default DB **psql image** inside Docker container
* Tools: **OWASP ZAP** 2.15.0
* Network: localhost

### **Assumptions & constraints:**

* Application behavior is limited to what is exposed by the Docker containers.
* No internal code-level testing was possible (black-box only).
* Automated tests may not reveal logical or business-logic vulnerabilities.

---

# 2️⃣ **Executive Summary**

### **Short summary:**

Testing revealed several **high- and medium-severity vulnerabilities**, including **path traversal, potential SQL injection, missing CSRF protection, missing security headers, and unsafe format string processing**, making the application highly vulnerable to attacks.

---
### **Overall risk level:**

🔴 **HIGH**

### **Top 5 immediate actions:**

1. Implement strict server-side input validation and sanitization (block `../`, quotes, format specifiers, etc.).
2. Use prepared statements to prevent SQL Injection.
3. Add CSRF protection tokens to all POST forms.
4. Implement missing security headers: CSP, X-Frame-Options, X-Content-Type-Options.
5. Prevent path traversal by rejecting dangerous characters (`../`, `/`, `\`).

---



# **3️⃣ Severity scale & definitions**

| Severity Level | Description | Recommended Action |
| -------------- | ------------------------------------------------------------------------------------------------- | -------------------------- |
| 🔴 **High** | A serious vulnerability that can lead to system compromise (e.g., SQL Injection, Path Traversal). | **Immediate fix required** |
| 🟠 **Medium** | A significant issue requiring certain conditions (e.g., CSRF, missing headers). | **Fix ASAP** |
| 🟡 **Low** | Minor issue or configuration weakness. | **Fix soon** |
| 🔵 **Info** | No direct risk but useful for hardening. | **Monitor** |

---

# **4️⃣ Findings (Top 5)**

| ID | Severity | Finding | Description | Evidence / Proof |
| -------- | --------- | ------------------------------------- | --------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| **F-01** | 🔴 High | **Path Traversal Vulnerability** | Input fields accept traversal payloads (`../`), indicating backend does not sanitize paths. | ZAP alert: *Path Traversal* (username parameter) fired with payload: ../../etc/passwd |
| **F-02** | 🔴 High | **Possible SQL Injection** | Registration form reacts to `' OR '1'='1` and similar payloads with 500 errors, suggesting unsafe SQL handling. likely does not use parameterized queries | ZAP alert: *SQL Injection* (username) |
| **F-03** | 🟠 Medium | **Absence of Anti-CSRF Token** | Registration form does not include CSRF token, allowing forged submissions. Attackers could submit unauthorized registrations . | ZAP alert: *No Anti-CSRF Token* on /register |
| **F-04** | 🟠 Medium | **Missing Critical Security Headers** | CSP, X-Frame-Options,Referrer-Policy,Permissions-Policy and X-Content-Type-Options are missing across multiple pages. | ZAP alerts: *X-Frame-Options Header Not Set*, *Missing CSP Header*, *X-Content-Type-Options Missing* |
| **F-05** | 🟠 Medium | **Format String Vulnerability** | Input such as `%s`, `%n`, `%x` is processed unsafely, indicating poor string handling. | ZAP alert: *Format String Error* on registration endpoint 

---


# **5️⃣ OWASP ZAP Test Report (Attachment)**


[OWASP ZAP scan results ](https://github.com/prahladtimalsina/Cybersecurity-And-Data-Privacy-Autumn2025/blob/main/BookingSystem/phase1/Zap_report_prahlad-2025-11-16-.md)

---
[Attach full report](https://github.com/prahladtimalsina/Cybersecurity-And-Data-Privacy-Autumn2025/blob/main/BookingSystem/phase1/part1/report_template.md)






 