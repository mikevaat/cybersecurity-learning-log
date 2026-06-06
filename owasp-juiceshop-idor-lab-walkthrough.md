# Lab Walkthrough: Insecure Direct Object Reference (IDOR) - OWASP Juice Shop

## Overview
In this lab, we explore a classic Broken Access Control vulnerability known as **Insecure Direct Object Reference (IDOR)** within the OWASP Juice Shop application.

### What is IDOR?
IDOR occurs when an application provides direct access to objects based on user-supplied input without properly verifying if the user has the authorization to access that specific object. Imagine a coat check at a restaurant:
- You are given ticket #6.
- When you leave, you hand the attendant ticket #6, but you cross it out and write #1 instead.
- If the attendant simply grabs coat #1 without checking if it actually belongs to you, that's an IDOR vulnerability.

In the context of web applications, these "tickets" are often database IDs, account numbers, or filenames passed in the HTTP request. If the server doesn't validate our session token against the requested resource, we can view or manipulate data belonging to other users.

### Objective
Successfully view another user's shopping basket by manipulating the API request parameters.

### Tools Used
- *OWASP Juice Shop* (Target environment)
- *Burp Suite Community Edition* (Web Proxy)

---


# Step-by-Step Execution

### Step 1: Authentication and Baselining
Before we can break the application, we need to understand how it behaves normally.
- I fired up the Juice Shop instance and navigated to the login page.
- I authenticated using standard test credentials: `test@test.com`.
- By logging in, the application assigns my session a unique authentication token and associates my user profile with a specific shopping basket ID in the backend database.


![](images/idor_lab_initial_login.png)

<br>

### Step 2: Generating Legitimate Traffic
To capture the API endpoint responsible for handling shopping baskets, I needed to trigger the functionality natively.
- Navigated to the product catalog.
- Clicked **Add to Basket** under the Apple Juice product. This step was done to play a simple trick on the browser and essentially confuse it.
- Then clicked on **Your Basket** to view my cart.
This establishes our baseline. We now know what a legitimate request to view a basket looks like from the application's perspective. This step makes the whole traffic-capture more stealtheir and natural from the server’s perspective. 

![](images/idor_lab_add_to_basket_button.png)

<br>

### Step 3: Intercepting the Request with Burp Suite
With baseline established, it was time to look under the hood at actual HTTP traffic.
- Switched over to Burp Suite and navigated to the Proxy tab.
- Toggled **Intercept** on to catch outgoing traffic before it reached the server.

  ![](images/idor_lab_turning_burp_intercept_on.png)

  <br>
  
- Refreshed my shopping basket page; Burp caught this outgoing request.

![](images/idor_lab_burp_proxy_intercept.png)

<br>

**Looking at the raw HTTP data revealed:**
- The browser was making a GET request to `/rest/basket/6` which indicates:
- The application uses predictable sequential integers for Basket IDs.
- My current session is tied to Basket ID 6.

### Step 4: Transitioning to Repeater for Manipulation
**To safely test variables without refreshing repeatedly:**
- I sent the captured request to Burp's Repeater (`Right-click -> Send to Repeater` or `CTRL + R`).
- In Repeater, reading through the body, we see more information; such as the authorization token, cookie token, and so much more. This actively confirms the baseline works as expected with Basket 6 contents.

![](images/idor_lab_repeater_request.png)

<br>

### Step 5: Executing Exploit
**Testing IDOR vulnerability:** <br>
I modified the default URL, which was `/rest/basket/6`, in Repeater to `/rest/basket/1` (typically belonging to another user). <br>
Sent the request again;
the server returned `200 OK`, revealing products added by another user, which was also an apple juice. 

![](images/idor_lab_repeater_request_mod_alt.png)

<br>

Proof of exploit!


### Step 6: Browser Verification
To verify in-browser behavior, built-in tools in Burp were used (`Request in browser -> In current browser session`).

![](images/idor_viewing-basket_lab_json.png)

Navigating directly confirmed that manipulated URL displayed victim's basket JSON output, evidencing direct URL manipulation works while session token remains valid.

### Step 7: Confirmation & Validation
The Juice Shop Score Board recognized unauthorized access and marked the **View Basket** challenge as completed, confirming successful IDOR exploitation.

![](images/idor_viewing_basket_lab_solved.png)

<br>


---

# Key Takeaways & Remediation
This lab explicitly demonstrates why client-side input cannot be trusted. The vulnerability existed because:
a) Server checked login status but not specific identity before serving resources;
b) Proper access controls were missing. 

**Remediation strategies:**
a) Use unpredictable resource identifiers such as UUIDs or cryptographic hashes instead of sequential integers;
b) Backend must verify that requesting user has explicit permission for each resource via their session token.
