# Authentication Endpoint Analysis & NoSQL Injection Testing

## Overview
During dynamic analysis of a local OWASP Juice Shop instance, I conducted targeted injection testing against the primary authentication endpoint. The objective was to evaluate the application's susceptibility to NoSQL injection (NoSQLi) and analyze how the backend handles unexpected JSON object structures within the authentication flow.

## Environment
- **Application:** OWASP Juice Shop
- **Target Endpoint:** POST /rest/user/login
- **Observed Stack:** Node.js backend utilizing the Express.js framework.
- **Testing Methodology:** Traffic interception and payload manipulation via Burp Suite.

## Methodology & Observations
1. **Baseline Request Capture**
   - To establish a baseline, I initiated a standard login sequence using dummy credentials (`admin` / `pass123`). <br>
   - I then turned the intercept on in burp, navigated to the website and captured the post parameter. The post request was intercepted via the proxy and forwarded to the Repeater module for baseline structural analysis. 


![](images/nosqli_lab_intercept_on.png)
 
![](images/nosqli_lab_post_parameter_capture.png)

![](images/nosqli_lab_post_in_repeater.png)

 <br>
     
2. **Dual-Parameter NoSQL Injection Attempt**
   - The initial exploitation attempt involved replacing both the email and password string values with the NoSQL Greater-Than (`$gt`) operator, formatted as JSON objects.
   - Payload: `{"email": {"$gt": ""}, "password": {"$gt": ""}}`
   - Result: 500 Internal Server Error


![](images/nosqli_lab_post_in_repeater_err.png)

<br>

     
3. **Targeted Password Field Injection (`$gt` Operator)**
   - To isolate the failure point, the email parameter was reverted to a known-valid string format (`admin@juice-sh.op`), while retaining the NoSQL `$gt` operator on the password field.
   - Payload: `{"email": "admin@juice-sh.op", "password": {"$gt": ""}}`
   - Result: 500 Internal Server Error

![](images/nosqli_lab_post_in_repeater_err_2.png)

<br>

  
4. **Alternative Operator Testing (`$ne` Operator)**
   - Continuing focus on the password parameter, I tested the Not-Equal (`$ne`) operator to determine if the backend was filtering specific MongoDB keywords.
   - Payload: `{"email": "admin@juice-sh.op", "password": {"$ne": "wrong_password"}}`
   - Result: 500 Internal Server Error

![](images/nosqli_lab_post_in_repeater_err_4.png)

<br>

  
5. **Targeted Email Field Injection**
   - Since object injection against the password field consistently caused server faults, I pivoted to injecting the `$gt` operator into the email field while passing a standard string to the password field.
   - Payload: `{"email": {"$gt": ""}, "password": "invalid_password"}`
   - Result: 401 Unauthorized

![](images/nosqli_lab_post_in_repeater_err_3.png)

<br>


## Technical Interpretation
The observed responses provide deep insight into the backend architecture and its handling of authentication requests:
- **Cryptographic Type Validation Fault:** The consistent 500 Internal Server Error responses on password injections included this stack trace:
  > `TypeError [ERR_INVALID_ARG_TYPE]: The "data" argument must be of type string... Received an instance of Object.`
  
This indicates that	the Node.js crypto module (specifically Hash.update()) is intercepting the payload. The application attempts to hash the provided password before interacting with the database. Because it receives a JSON object instead of a string, it triggers a fatal runtime error.


- **Structural Defense-in-Depth:** The application inherently defends against NoSQL object injection on the password parameter because data never reaches the database query layer; it crashes at the application layer during hashing.
- **Logical Evaluation via Email Injection:** When shifting payloads to email (resulting in 401 Unauthorized), server did not crash, implying successful parsing but failed authentication. This suggests different processing for email versus password parameters without leading to an authentication bypass in this test case.

## Conclusion
Based on response behavior, `/rest/user/login` endpoint is built on Node.js/Express architecture and relies heavily on strict data type enforcement—either intentionally or as a byproduct of cryptographic functions. While accepting JSON, malformed object structures trigger application-layer crashes rather than database manipulation.
This underscores critical differences between application-level validation failures (TypeErrors) and potential database-layer exploits. Further testing with relational SQL syntax (e.g., SQLite payloads) may be necessary since no standard NoSQL vulnerabilities are evident here.
