# Vulnerability Research: Authentication Bypass via SQL Injection

**Target:** OWASP Juice Shop (Lab Environment)  
**Severity:** Critical  
**Vulnerability Type:** SQL Injection (SQLi) leading to Authentication Bypass  

---

## 1. Executive Summary

During a routine security assessment of the OWASP Juice Shop application, I identified a critical SQL Injection vulnerability within the login component. By manipulating the backend SQL query through the email input field, I was able to bypass the authentication mechanism and gain unauthorized access to the administrative account without a valid password.

---

## 2. Reconnaissance & Initial Observation

The first step was to observe how the application handles standard authentication traffic. Using Burp Suite Professional, I intercepted a legitimate login attempt to map the API endpoint and the data structure being sent to the server.

- **Endpoint:** `POST /rest/user/login`  
- **Payload Type:** JSON  
- **Observation:** The application communicates via a RESTful API, sending credentials in a structured JSON format.  

![initial_login_attemt](images/owasp-lab-brup-login-bypass-normal-login.png)


![POST_request_recieved](images/owasp-lab-login-credentials-logged-on-burp.png)

- **Furthermore, I sent the raw payload including the login credentials to the Repeater to parse it.**
![credentials_sent_for_parsing](images/owasp-lab-login-credentials-sent-to-repeater(2).png)
  
---

## 3. Fuzzing & Error Detection

To test for SQL Injection, I injected a single quote (`'`) into the email field to break the syntax of the underlying SQL statement.

The request was forwarded to Burp Repeater for deeper analysis. The server responded with a `500 Internal Server Error`, leaking a stack trace that confirmed the use of SQLite and the Sequelize ORM.

![I_added_a_wrongly_formatted_sql_query_for_testing](images/owasp-lab-added-'-to-the-test-credentials-to-give-a-sqlite-error-also-an-indicator-of-sql-injection.png)

---

## 4. Exploitation: Crafting the Payload

Based on the observed behavior, the backend query likely resembled:

```sql
SELECT * FROM Users WHERE email = 'USER_INPUT' AND password = 'PASSWORD_INPUT';
```
I crafted a tautology-based payload:

test' OR 1=1--

### Logic Breakdown

- `test'` → Closes the original string for the email field  
- `OR 1=1` → Always-true condition  
- `--` → Comments out password verification  

![SQL_statement_crafting_process](images/owasp-lab-crafting-the-sql-injection-statement.png)

Resulting query:

SELECT * FROM Users WHERE email = 'test' OR 1=1; --' AND password = '...';

---

## 5. Execution & Bypass

I injected the payload into the email field of the login form. Since `1=1` is always true, the database returns the first record in the Users table—which is typically the Administrator account.

![Injecting payload into login form](images/owasp-lab-injecting-statement.png)

---

## 6. Post-Exploitation & Proof of Concept (PoC)

The injection succeeded. The application issued a session token for the administrator account, and the "Login Admin" challenge was marked as solved.

This confirms that an attacker could gain full administrative control over the platform without valid credentials.

![Successful bypass confirmation](images/owasp-lab-sql-injection-successfull.png)

---

## 7. Remediation Recommendations

- Use Prepared Statements (Parameterized Queries)  
- Enforce strict input validation (allow-listing email format)  
- Disable verbose error messages in production  
- Ensure ORM queries (Sequelize) do not concatenate raw input  

---

## Report Details

- **Date:** April 16, 2026  
- **Tools Used:** Burp Suite, Kali Linux, OWASP Juice Shop
