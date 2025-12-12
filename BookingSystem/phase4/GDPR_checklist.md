# GDPR Compliance Checklist – Web-based Booking System

<!-- ## System Description
- Web-based booking system accessed via a browser.  
- Users can register and log in.  
- Roles: **Reserver** or **Administrator**.  
- Administrators can add, modify, and delete resources and reservations, and delete reservers.  
- Reservers can book resources only if **≥15 years old**.  
- Resources are bookable on an hourly basis.  
- Booked resources are publicly visible **without login**, but reserver identity is hidden.  
- APIs `/api/users`, `/api/reservations`, `/api/resources` are **publicly accessible**, causing GDPR risks.  
- System follows **Privacy by Design (PbD)** principles.  
- Registration includes acceptance of **Terms of Service**
- Homepage provides links to **Privacy Policy, Terms of Service, and Cookie Policy**. -->

---

# 1. Personal Data Mapping and Minimization

| **Result** | **Item** |
| :----: | --- |
| ⚠️ | Personal data identified: email, username, DOB, password hash, user_token. `/api/users` exposes usernames/emails publicly → privacy risk. |
| ⚠️ | `/api/reservations` exposes reservation tokens and resource IDs publicly. |
| ⚠️ | `/api/resources` public API may expose metadata that could indirectly identify users. |
| ✅ | Only necessary personal data is collected for registration and booking. |
| ✅ | Age stored as DOB to verify bookers ≥15 years old. |

---

# 2. User Registration and Management

| **Result** | **Item** |
| :----: | --- |
| ✅ | Registration includes GDPR-compliant consent and Terms of Service acceptance (checkbox ticked). |
| ⚠️ | Users cannot edit/delete their own data yet (profile page missing). |
| ❌ | Right to be forgotten not fully implemented (logs/backups not deleted). |
| ✅ | Age restriction enforced via DOB check (≥15). |

---

# 3. Booking Visibility

| **Result** | **Item** |
| :----: | --- |
| ✅ | Reservations visible without login only at resource level; reserver identity hidden. |
| ❌ | `/api/users` exposes usernames/emails publicly → GDPR violation. |
| ❌ | `/api/reservations` and `/api/resources` are public APIs → potential privacy/data exposure. |


---

# 4. Access Control and Authorization

| **Result** | **Item** |
| :----: | --- |
| ❌ | Reservers can modify others’ reservations → weak authorization. |
| ⚠️ | Roles exist (guest/reserver/admin), but enforcement is inconsistent. |
| ⚠️ | Admin privileges not fully limited → potential data misuse. |
| ❌ | Public APIs allow data access without authentication → GDPR violation. |


---

# 5. Privacy by Design (PbD)

| **Result** | **Item** |
| :----: | --- |
| ❌ | PbD claim is partially unfulfilled (public user list + weak access control). |
| ⚠️ | Logs may contain unnecessary user data (login logs store IPs). |
| ✅ | Login forms use CSRF tokens. |

---

# 6. Data Security

| **Result** | **Item** |
| :----: | --- |
| ⚠️ | Some protections exist, but XSS/SQLi defenses not fully verified. |
| ✅ | Passwords stored as bcrypt hashes. |
| ⚠️ | No documented GDPR-compliant backup/recovery processes. |
| ⚠️ | No confirmation that personal data is stored exclusively within the EU. |

---

# 7. Data Anonymization and Pseudonymization

| **Result** | **Item** |
| :----: | --- |
| ✅ | Reservation visibility hides user identity (tokenized). |
| ❌ | No pseudonymization applied to emails, IPs, or user metadata in logs. |
| ⚠️ | Public APIs expose tokens and metadata without anonymization. |


---

# 8. Data Subject Rights

| **Result** | **Item** |
| :----: | --- |
| ❌ | Users cannot download/export their data (no access request function). |
| ❌ | Users cannot delete their account (right to be forgotten not fully implemented). |
| ❌ | Users cannot withdraw consent after registration. |

---

# 9. Documentation and Communication

| **Result** | **Item** |
| :----: | --- |
| ✅ | Privacy Policy accessible via homepage link during registration and elsewhere. |
| ✅ | Terms of Service accessible via homepage link. |
| ✅ | Cookie Policy accessible via homepage link. |
| ❌ | No internal documentation for personal data handling provided. |
| ❌ | No data breach notification process documented. |

---

# Final Evaluation Summary

| Category | Status |
|---------|--------|
| Personal Data | ⚠️ |
| User Rights | ❌ |
| Booking Visibility | ⚠️ |
| Access Control | ❌ |
| Privacy by Design | ❌ |
| Data Security | ⚠️ |
| Documentation | ⚠️ |

---

# Major GDPR Violations Identified

1. **Public `/api/users` endpoint exposes usernames/emails** → critical GDPR breach.  
2. **Registration includes consent/ToS (✅)** — improvement.  
3. **Users cannot access, edit, delete, or export their personal data.**  
4. **Weak authorization allows users to modify others’ reservations.**  
5. **Right-to-be-forgotten mechanism not implemented.**  
6. **PbD claim partially unfulfilled due to public data exposure.**  
7. **No breach notification or data-processing documentation.**  
8. **Login IPs stored without pseudonymization → personal data risk.**
9. **Public `/api/reservations` and `/api/resources` APIs** allow access without authentication. 

---

**Symbols Used:**  
- ✅ Pass  
- ❌ Fail  
- ⚠️ Needs attention
