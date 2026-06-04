# BOLA Lab Walkthrough: Manipulate Basket (OWASP Juice Shop)

**Author:** CypherX  
**Target:** OWASP Juice Shop  
**Vulnerability:** Broken Object Level Authorization (BOLA) / HTTP Parameter Pollution

---

# Executive Summary

In this engagement, we escalated a standard Broken Access Control vulnerability into a functional data manipulation exploit. While previous labs focused on simply viewing another user's data, this lab requires us to actively alter a database record belonging to another user.

This walkthrough documents the methodology of bypassing server-side validation checks using a combination of Mass Assignment and HTTP Parameter Pollution (HPP) to successfully force an item into an unauthorized user's shopping basket.

---

# Core Concepts Explained

Before diving into the execution, it's crucial to understand the mechanics of why this exploit works:

## 1. BOLA (Broken Object Level Authorization)

Think of this like a hotel keycard. The front desk checks your ID and gives you a keycard for Room 10. BOLA occurs when you figure out how to mathematically rewrite your keycard to open Room 11, and the lock on Room 11 doesn't verify if the name on the card matches the room's owner.

The server trusts the object ID (the room number) provided by the client.

---

## 2. HTTP Parameter Pollution (HPP)

This happens when you send multiple parameters with the exact same name in a single HTTP request. Some backends process the first one they see, while the database might process the last one.

An attacker can leverage this inconsistency in parameter handling.

---

## 3. Mass Assignment

Web frameworks often take raw JSON data from a request and map it directly to database columns. If developers don't explicitly blacklist fields (like `basketId`), an attacker can inject those fields into the payload and overwrite database values.

---

# Step-by-Step Execution

# Phase 1: Authentication and Baselining

Every solid pentest starts with understanding the application's normal behavior.

1. I initialized the Juice Shop environment and intercepted traffic using Burp Suite.

2. I authenticated using a standard test account:

   ```
   test@test.com
   ```
   
![](images/bola_lab_initial_dummy_login.png)


3. Navigating to the product catalog, I initiated an "Add to Basket" action to capture the raw API call.

![](images/bola_lab_add_to_basket.png)


4. In Burp Suite, I captured the initial:

   ```http
   POST /api/BasketItems/
   ```

   request.

![](images/bola_lab_post_request.png)

5. I forwarded this request to the Repeater tab to manipulate the data safely.

![](images/bola_lab_post_request_sent_to_repeater.png)


6. Scrolling down to the JSON body, I observed my baseline configuration:

   ```json
   "BasketId": "6"
   ```
![](images/bola_lab_post_request_sent_to_repeater_json.png)

   My session was assigned to Basket ID 6.

<br>

---

# Phase 2: Methodology and Troubleshooting Process.

Exploitation is rarely a straight line. The initial assumption with BOLA is that we can simply change the target ID.

## Direct Manipulation

I modified the JSON payload to target a different user by changing:

```json
"BasketId": "6"
```

to:

```json
"BasketId": "1"
```

![](images/bola_lab_post_request_sent_to_repeater_json_mod.png)


---

## The Authorization Validation Failure

The server returned a strict:

```http
401 Unauthorized
```

![](images/bola_lab_post_request_sent_to_repeater_json_mod_response_error.png)


The application's validation engine was successfully comparing my:

```http
Authorization: Bearer
```

token against the `BasketId` I was trying to modify.

---

## Auth Stripping (Failed)

I attempted to bypass the check by completely removing the Authorization header, hoping the API would fail open.

The server still rejected the request.

![](images/bola_lab_repeater_json_error_troubleshooting.png)


---

## Database Conflicts (500 Errors)

During testing, I encountered:

```http
500 Internal Server Error
```

responses, specifically:

```text
SequelizeUniqueConstraintError
```

This was a significant indicator.

It meant my payloads were reaching the database, but failing because I was trying to add a Product ID that already existed in the target basket.

I needed to use fresh Product IDs (like `11` or `25`) to proceed.

---

# Phase 3: The Exploit (Parameter Pollution)

Since the server strictly validated the `BasketId` against my token, the objective was to satisfy the authorization validation while influencing how the backend processed the request data.

To bypass this control, a specially crafted payload was constructed.

I crafted a payload that utilized HTTP Parameter Pollution to sandwich the target ID alongside my legitimate ID.

## 1. Payload Construction

```json
{
  "ProductId": 25,
  "BasketId": "6",
  "quantity": 1,
  "BasketId": "1",
  "basketId": "1"
}
```

![](images/images/bola_lab_post_request_sent_to_repeater_json_response_mod_payload2.png)


> [!IMPORTANT]
> I also successfully verified this exploit using `ProductId: 11` to bypass or avoid any database constraint conflicts.
> ![](images/bola_lab_post_request_sent_to_repeater_json_response_mod.png)


---

## 2. The Logic

### Security Validator

The security validator reads the first:

```json
"BasketId": "6"
```

It matches my session token.

**Access Granted.**

### Database Layer

The request is subsequently processed by the database layer.

Due to a Mass Assignment vulnerability, the database prioritizes the last parameter it receives—or the lowercase `basketId` schema—which we set to:

```json
"1"
```

---

## 3. The Execution

I sent the request.

The server returned:

```http
HTTP/1.1 200 OK
```

The response confirmed successful insertion into BasketId 1.

```json
"BasketId": "1"
```

We had successfully inserted an item into another user's basket.

---

# Phase 4: Verification & The Accidental Discovery

Returning to the browser to verify the exploit on the Score Board, two things happened:

## Challenge Completion

The **"Manipulate Basket"** challenge successfully turned green, validating the BOLA exploit.

![](images/bola_lab_solved_2.png)

<br>

![](images/bola_lab_solved.png)




---

## Unintended Discovery

Because of the extensive URL and API manipulation during the troubleshooting phase, I inadvertently triggered and solved the hidden **"Score Board"** challenge, effectively knocking out two flags in one session.

![](images/hidden_scoreboard_found.png)

---

# Remediation & Developer Recommendations

To prevent this vulnerability, developers must ensure security checks and database operations use the exact same data source.

## 1. Strict Input Validation

The API must reject requests containing duplicate parameters (Parameter Pollution) rather than silently processing them.

---

## 2. Data Transfer Objects (DTOs)

Avoid binding raw HTTP request data directly to database models (Mass Assignment).

Only allow specific, whitelisted fields to be updated by the user.

---

## 3. Consistent Authorization

The `BasketId` should ideally be inferred entirely from the secure, server-side session token, rather than relying on a client-supplied JSON parameter.

---

# Conclusion

This lab demonstrates how seemingly separate weaknesses—Broken Object Level Authorization, HTTP Parameter Pollution, and Mass Assignment—can be chained together to bypass intended security controls and manipulate another user's data.

The success of the exploit hinged on understanding how different application layers interpreted duplicate parameters differently, allowing the authorization layer and database layer to operate on conflicting values.
