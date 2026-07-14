# Lab Report: DOM-Based Cross-Site Scripting (XSS) Mitigation & Exploitation

**Target:** OWASP Juice Shop  
**Vulnerability Category:** Injection (XSS)  
**Classification:** DOM-Based


## Executive Summary

This lab demonstrates the identification and exploitation of a DOM-based Cross-Site Scripting vulnerability within a web application's search functionality. By injecting a malicious script into the client-side environment, I successfully bypassed standard input expectations to execute arbitrary JavaScript within the victim's browser context.

---
## ○ Technical Methodology & Procedures
|   **Step 1: Reconnaissance and Fingerprinting**
      Before attempting an injection, it is critical to understand the application's structure. I started by examining the files and general directory structure to identify how the application handles routing and client-side scripts.
         - *Observation:* The application utilizes a modern JavaScript framework (Angular), which heavily relies on client-side rendering—a common breeding ground for DOM XSS.

![Initial_Enumeration](images/owasp-lab-owasp-search-input-normal-1.png)

|    **Step 2: Testing Input Reflection (HTML Injection)**
       I began testing the search bar to see if it sanitized input before rendering it back to the page. I used a simple HTML tag `<h1>owasp` to check if the browser would interpret the tag or treat it as plain text.
         - **Result:** The application rendered the text in a Heading 1 format, confirming that the input is reflected directly into the DOM without proper encoding.

![HTML_Injection_Attempt](images/owasp-lab-owasp-search-input-normal.png)


|    **Step 3: Crafting the XSS Payload**
      Knowing that the DOM is vulnerable to reflection, I moved from simple HTML to a script-based payload. I chose a payload that uses an `<iframe>` with a `javascript:` URI to trigger an alert box. This is a classic "Proof of Concept" (PoC) to demonstrate execution.
         - **Payload Used:**
```html
<iframe src="javascript:alert(`xss`)">
```

![Crafting_The_XSS_Payload](images/owasp-lab-dom-based-xss-complete.png)


|     **Step 4: Execution and Verification**
        After submitting the payload, the application processed the string and injected the `<iframe>` into the page's search result section. Since the source of the frame was a JavaScript alert, the browser executed it immediately.
          - **Indicator of Success:** A browser alert box appearing with the string `xss`, confirming that arbitrary code execution was achieved.


![Indicator_Of_Success](images/owasp-lab-alert-box-popup-after-injecting-a-script.png)


|     **Step 5: Challenge Confirmation**
        The Juice Shop internal tracking confirmed the successful exploit of the DOM XSS challenge. This validates that the vulnerability was not just a visual glitch but a genuine flaw in the client-side code handling the search query.


## ○ Root Cause Analysis
The vulnerability exists because the application takes a user-controlled input (the search term) and passes it to a "sink" (a function or DOM property like `innerHTML`) that allows script execution, without first performing proper sanitization or using safer alternatives like `textContent`.

## ○ Remediation Recommendations
To mitigate DOM-based XSS, the following controls should be implemented:
- **Use Safe Sinks:** Avoid using `innerHTML` or `document.write()`. Instead, use `element.textContent` or `element.innerText`, which treats all input as literal text rather than executable code.
- **Input Validation:** Implement a strict allow-list for expected characters in the search bar.
- **Content Security Policy (CSP):** Deploy a robust CSP header to prevent inline scripts from executing and restrict sources from which scripts can be loaded.

---
**Analyst:** cypherx  
**Environment:** Kali Linux / OWASP Juice Shop Lab  
**Date:** April 2026
