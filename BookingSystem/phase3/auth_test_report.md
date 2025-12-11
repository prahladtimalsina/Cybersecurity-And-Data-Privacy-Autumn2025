
# PHASE 3 – Authorization Test Report


**This document describes what Guest, Reserver, and Administrator users can and cannot do in the current implementation, based on real browser testing and API requests. It highlights discrepancies between the specification and actual behavior, as well as potential security issues.**

---
## 1) Guest User  
### ✔ Guest Can Do

| Action                       | Route                   | Notes                                |
| ---------------------------- | ----------------------- | ------------------------------------ |
| View public homepage         | `GET /`                 | Public page                          |
| View resource list           | `GET /api/resources`    | No login required                    |
| View reservations list (API) | `GET /api/reservations` | Public access, identity hidden       |
| Access login page            | `GET /login`            | Shows login form with CSRF           |
| Submit login                 | `POST /login`           | User can attempt authentication      |
| Access registration page     | `GET /register`         | Shows registration form              |
| Submit registration          | `POST /register`        | Can create user                      |
| Access static assets         | `GET /static/*`         | Assets like CSS, JS, images          |
| Access user listing (BUG)    | `GET /api/users`        | Allowed by mistake, no session check |

--- 
### 🚫 Guest Cannot Do

| Action Forbidden                 | Route                                                  | App Behavior                                |
| -------------------------------- | ------------------------------------------------------ | ------------------------------------------- |
| Access reservation form          | `GET /reservation`                                     | `303 → /status.html?failed=Unauthorized`    |
| Create a reservation             | `POST /reservation`                                    | Redirects to failure due to missing session |
| View session details             | `GET /api/session`                                     | Redirects to failure page                   |
| Modify reservations              | `PATCH/PUT/DELETE /api/reservations/:id`               | Blocked via redirect                        |
| Access admin/resource management | `POST /api/resources`, `PUT/DELETE /api/resources/:id` | Not permitted                               |

### 🔎 Important Finding

`Spec violation: Guests should not list users, but current code allows GET /api/users without authentication.`


## 2) Reserver User

**A registered user with role = reserver.**




| Action                       | Route                             | Notes                                                    |
| ---------------------------- | --------------------------------- | -------------------------------------------------------- |
| Login / Register             | `POST /login`, `POST /register`   | Works normally                                           |
| Access reservation form      | `GET /reservation`                | Allowed with session                                     |
| View homepage and resources  | `GET /`, `GET /api/resources`     | Same as guest                                            |
| View reservation list        | `GET /api/reservations`           | Public data, identity hidden                             |
| Create a reservation         | `POST /reservation`               | Needs session                                            |
| Delete their own reservation | `DELETE /api/reservations/:id`    | Allowed if owner                                         |
| Age restriction applies      | `>= 15 years`                     | Reserver must be old enough                              |
| View user list (BUG)         | `GET /api/users`                  | Allowed mistakenly                                       |
| Update reservation fields    | `PATCH/PUT /api/reservations/:id` | Can change resource, date, and username (security issue) |


---
### 🚫 Reserver Cannot Do


| Action Forbidden                      | Route                                                  | Behavior             |
| ------------------------------------- | ------------------------------------------------------ | -------------------- |
| Manage system resources               | `POST/PUT/DELETE /api/resources*`                      | Should require admin |
| Delete or modify others’ reservations | Should be blocked but **currently not enforced fully** |                      |



### ⚠ Security Issue

`Reserver can update usernames and reservations of others, due to weak authorization checks.`

---

## 3) Administrator User

### User with role = administrator.



| Action                             | Route                                                                          | Notes                            |
| ---------------------------------- | ------------------------------------------------------------------------------ | -------------------------------- |
| All guest + reserver abilities     | `All of the above`                                                             |                                  |
| Create reservation                 | `POST /reservation`                                                            | Session required                 |
| Delete any reservation             | `DELETE /api/reservations/:id`                                                 | Admin override                   |
| Modify any reservation             | `PUT/PATCH /api/reservations/:id`                                              | Full permission                  |
| Access reservation form            | `GET /reservation`                                                             | Allowed                          |
| Manage resources                   | `POST /api/resources` / `PUT /api/resources/:id` / `DELETE /api/resources/:id` | Create, update, delete resources |
| View resource detail without login | `GET /api/resources/:id`                                                       | Public by design                 |


### 🚫 Administrator Cannot Do

| Action                        | Expected                           | Current Behavior       |
| ----------------------------- | ---------------------------------- | ---------------------- |
| Nothing meaningful restricted | Admin expected to have full access | Matches implementation |



## 4) Code-Level Findings (Security Analysis)


| Issue                                           | Impact                                                  | Affected Roles   |
| ----------------------------------------------- | ------------------------------------------------------- | ---------------- |
| `GET /api/users` has no authentication          | Anyone can list users (GDPR/privacy risk)               | Guest + Reserver |
| Reservations API lacks ownership validation     | Users can modify others’ bookings                       | Reserver         |
| Admin checks missing for Resources API          | Any logged-in user could escalate with crafted requests | Reserver         |
| API redirects instead of JSON errors            | Hard to automate API testing                            | All              |
| Public `/api/resources/:id` may expose metadata | Info disclosure risk                                    | Guest            |
---




# Discovery of hidden endpoints

### Directory discovery:

## Command   

```bash
gobuster dir -u http://localhost:8003 -w /usr/share/seclists/Discovery/Web-Content/raft-small-directories.txt -b 303

```



**Screenshot 1:** ![alt text](https://raw.githubusercontent.com/prahladtimalsina/Cybersecurity-And-Data-Privacy-Autumn2025/refs/heads/main/BookingSystem/phase3/images/gobuster1.png)


**Screenshot 2:** ![alt text](https://raw.githubusercontent.com/prahladtimalsina/Cybersecurity-And-Data-Privacy-Autumn2025/refs/heads/main/BookingSystem/phase3/images/gobuster1.png)


### API endpoint discovery:

## Command   



```bash
ffuf -u http://localhost:8003/api/FUZZ \                                                         
-w /usr/share/seclists/Discovery/Web-Content/raft-small-words.txt \
-fc 303 \
-fl 0 \
-o ffuf_api.json

```

All three detected paths returned Status: 200, not redirected
{ "FUZZ": "users", "status": 200, "length": 421 }
{ "FUZZ": "resources", "status": 200, "length": 283 }
{ "FUZZ": "reservations", "status": 200, "length": 608 }


**Screenshot:** ![alt text](https://raw.githubusercontent.com/prahladtimalsina/Cybersecurity-And-Data-Privacy-Autumn2025/refs/heads/main/BookingSystem/phase3/images/ffuf.png)