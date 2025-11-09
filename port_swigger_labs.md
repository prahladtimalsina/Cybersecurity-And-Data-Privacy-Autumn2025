# Portswigger labs

## Topic SQL injection 



### Lab: Sql injection vulnerability in WHERE clause allowing retrieval of hidden data

- I completed the lab by injecting the category filter to show unreleased products: I closed the category string, added OR 1=1 so the condition always passed, and used a comment to ignore the rest of the WHERE clause. I learned that SQL injection often targets string-built WHERE clauses and can easily bypass business rules. The hardest part was trying different payloads until one matched the app’s SQL syntax. The lab highlighted the importance of prepared statements, allowlists, and input validation.


### Lab: SQL injection vulnerability allowing login bypass

- I solved the login-bypass lab by entering administrator'-- in the username field. The application checks credentials by building a SQL query from the login inputs; my payload closed the username string and turned the rest of the WHERE clause into a comment, so the password check was ignored and I gained administrator access. I learned how fragile authentication is when SQL is built from raw input. The trickiest part was discovering which characters the form accepted. Simple fixes are parameterized/prepared statements and server-side input validation.

## Topic Authentication

### Lab: Username enumeration via different responses

- I completed the username-enumeration lab using Burp Suite. First I sent many usernames to the login endpoint and watched differences in server responses — a different response length/error told me which username was valid. With the username confirmed, I used Burp Intruder to brute-force the password list and spotted the successful attempt by a shorter response length, which revealed the password. I learned how easy it is to discover accounts from inconsistent responses. Mitigations are simple: return uniform error messages, add rate limiting/account lockouts, require CAPTCHAs for repeated attempts, and log/monitor suspicious activity.



### Lab: Password reset broken logic 

- I exploited a flaw in the password reset flow by changing the user parameter and requesting a token for my account. I then modified the token to x and submitted it with the target username carlos, which allowed me to set a new password for Carlos and log in. I learned tokens must be tied to and validated for a specific user. Fixes: use secure, server-stored single-use tokens, validate user-token binding, and avoid trusting client-supplied identifiers.

## Topic Access control

### Lab: Unprotected admin functionality

- I discovered and exploited an unprotected admin panel that allowed deletion of users. By changing the URL—trying endings like admin-panel, admin, or administrator—I was able to locate the administrator interface. Because access relied only on obscurity (hoping users wouldn’t try URL tampering), the panel was reachable simply by modifying the URL to administrator-panel. Once inside, I could use the admin functions to remove user accounts.


### Lab: User role modifiable in profile

- In this lab I discovered a vulnerability that let the client change their own roleid value. The app determined access rights at login but stored that role info on the client side and trusted it. By changing roleid from the default 1 to 2, I gained administrator privileges. The root cause was broken access control: never rely on client-side values for security — enforce roles and permissions on the server.