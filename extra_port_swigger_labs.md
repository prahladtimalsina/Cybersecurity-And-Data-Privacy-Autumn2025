
## Extra labs:
### Topic SQL injection
### Lab: ->SQL injection attack, querying the database type and version on Oracle
- I completed the lab by exploiting a SQL injection vulnerability to retrieve the database version. By injecting a UNION SELECT payload ('+UNION+SELECT+BANNER,+NULL+FROM+v$version--) into the vulnerable parameter, I was able to extract the version information from the Oracle database. The payload used was designed to match the number of columns in the original query, allowing me to successfully combine results. I learned that understanding the database structure and syntax is crucial for effective SQL injection attacks. The challenge was crafting the correct payload to avoid syntax errors. To prevent such vulnerabilities, developers should use prepared statements, parameterized queries, and input validation.



### Lab: ->SQL injection attack, querying the database type and version on MySQL and Microsoft
 - In this lab I learned how SQL injection can be used not only to bypass filters, but also to extract database information. I intercepted the product category request using Burp Suite and modified the parameter to inject a UNION SELECT query. First, I tested how many columns the original query returned and identified which columns accepted text values. After confirming that two text columns were present, I used a UNION-based payload to retrieve the database version. This showed how attackers can identify the database type and version when input is not properly handled. The challenge was ensuring the payload matched the original query structure. To mitigate such attacks, developers should implement prepared statements, use parameterized queries, and validate all user inputs.




## Topic Path traversal

### Lab: File path traversal, simple case
- In this lab I learned how a file path traversal vulnerability can allow access to sensitive files on the server. I used Burp Suite to intercept the request that loads a product image and modified the filename parameter. By changing the value to `../../../etc/passwd`, the application accessed a file outside the intended directory and returned its contents. This demonstrated how failing to validate file paths can expose critical system files. The lab highlighted the importance of proper input validation and restricting file access on the server.

### Lab: File path traversal, traversal sequences blocked with absolute path bypass
- In this lab I learned that even when directory traversal sequences are blocked, an application can still be vulnerable if absolute paths are not restricted. I used Burp Suite to intercept the request that retrieves a product image and modified the filename parameter to /etc/passwd. The server accepted the absolute path and returned the contents of the file. This showed that filtering only traversal patterns is not enough; applications must strictly control allowed file paths and access locations.


### Lab:File path traversal, traversal sequences stripped non-recursively
- In this lab I learned that simply stripping traversal sequences is not enough to prevent path traversal attacks. I used Burp Suite to intercept the request that loads a product image and modified the filename parameter. By using a crafted path with repeated patterns (....//....//....//etc/passwd), the application still resolved the path and returned the contents of the /etc/passwd file. This demonstrated how weak input sanitization can be bypassed and why proper path normalization and allowlists are required.

### Lab: File path traversal, traversal sequences stripped with superfluous URL-decode
- In this lab I learned that weak filtering combined with multiple URL-decoding steps can still allow path traversal. I intercepted the request for a product image using Burp Suite and modified the filename parameter. By encoding the traversal characters multiple times (..%252f..%252f..%252fetc/passwd), the application decoded the input again on the server side and resolved the path. As a result, it returned the contents of the /etc/passwd file. This showed why proper normalization and strict allowlists are essential.

### Lab: File path traversal, validation of start of path
- In this lab I learned that checking only the beginning of a file path is not sufficient to prevent path traversal attacks. I used Burp Suite to intercept the request that retrieves a product image and modified the filename parameter to start with a valid directory path followed by traversal sequences. Even though the path began with /var/www/images/, the application resolved the ../ segments and accessed a sensitive system file. As a result, the contents of /etc/passwd were returned. This highlighted the need for proper path normalization and strict server-side file access controls.

### Lab: File path traversal, validation of file extension with null byte bypass
- In this lab I learned that validating only the file extension does not fully protect against path traversal. I intercepted the request for a product image using Burp Suite and modified the filename parameter. By appending a null byte followed by a valid image extension (../../../etc/passwd%00.png), the application passed the extension check but treated the path as the system file. As a result, it returned the contents of /etc/passwd. This showed why applications must properly handle null bytes and enforce strict server-side path validation.



## Extra Topic Access control labs

### Lab:Unprotected admin functionality with unpredictable URL 
- In this lab I learned that hiding an admin panel behind an unpredictable URL does not provide real security. By reviewing the page source using Burp Suite or the browser’s developer tools, I found JavaScript code that revealed the admin panel URL. I then accessed the admin interface directly and used the available functionality to delete the user carlos. This demonstrated that security through obscurity is ineffective and that admin features must always be protected with proper authentication and authorization checks.

### Lab:User role controlled by request parameter
- In this lab I first logged in using the provided credentials wiener:peter. Initially, I could not access the admin panel at /admin. I then intercepted the login response using Burp Suite and observed that the application set a cookie with Admin=false. By changing this value to Admin=true, the application trusted the modified parameter and granted me administrative access. After that, I was able to load the admin panel and delete the user carlos. This lab showed why user roles must always be validated server-side and never controlled by client-side parameters.

### Lab:User ID controlled by request parameter 
- In this lab I logged in using the provided credentials wiener:peter and navigated to my account page. I observed that the page URL contained my username in the id parameter. I sent this request to Burp Repeater and modified the id value from my username to carlos. The application accepted the changed parameter and returned Carlos’s account information, including his API key. This demonstrated an insecure direct object reference (IDOR) issue, where user access is controlled by client-supplied parameters instead of proper server-side authorization checks.

### Lab:User ID controlled by request parameter, with unpredictable user IDs 
- In this lab I learned that even when user IDs are not easily guessable, access control can still be broken. I first found a blog post written by carlos and clicked on his name to identify his user ID from the URL. After logging in with the provided credentials and opening my own account page, I replaced my user ID in the id parameter with Carlos’s ID. The application accepted the change and displayed Carlos’s account details, allowing me to retrieve and submit his API key. This demonstrated that relying on unpredictable IDs is not sufficient for access control; proper server-side authorization checks are essential to ensure users can only access their own data.

### Lab:Reflected XSS into HTML context with nothing encoded
- In this lab I learned how reflected XSS vulnerabilities can be exploited when user input is not properly sanitized. I used Burp Suite to intercept a search request and modified the search parameter to include a script tag: `<script>alert(1)</script>`. When I submitted the request, the application reflected my input directly into the HTML response without encoding it, causing the script to execute in my browser. This demonstrated the importance of output encoding and input validation to prevent XSS attacks.

### Lab: Stored XSS into HTML context with nothing encoded
- In this lab I learned how stored XSS vulnerabilities can lead to persistent attacks when user input is saved and later rendered without proper sanitization. I used the comment form on a blog post to submit a comment containing a script tag: `<script>alert(1)</script>`. When I or any other user viewed the blog post, the application rendered my comment directly into the HTML response without encoding it, causing the script to execute in the browser. This highlighted the need for output encoding and input validation to prevent stored XSS attacks.

### Lab: DOM XSS in document.write sink using source location.search

- In this lab I learned how DOM-based XSS vulnerabilities can occur when user input is directly manipulated in the browser without proper sanitization. I entered a crafted payload into the search box that broke out of the img src attribute and injected a script: `"><svg onload=alert(1)>`. When the page loaded, the script executed in my browser, demonstrating a DOM XSS attack. This highlighted the importance of sanitizing and validating user input before using it in client-side scripts or HTML contexts.