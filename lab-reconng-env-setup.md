# OSINT Lab: Recon-ng Environment Setup

## Focus
- CLI Navigation
- Context Management & Work-around understanding 

## Objective
To demonstrate foundational command-line fluency and secure data handling practices my targets were to:
- Initialize the Recon-ng framework
- Use the help menu to understand the framework
- Work-around some basic example contexts.

---

## Execution Breakdown

### 1. Framework Initialization
The lab began by launching the Recon-ng framework within a Kali Linux environment. Upon boot, a minor connection timeout occurred (standard behavior when running offline or isolated VM network configurations), which was bypassed
```bash
recon-ng
```
<br>

![](images/recon_ng_lab_framework_startup.png)

<br>

### 2. Context Mapping & Syntax Verification
Before executing active commands, the global help menu was invoked. This is a critical habit to map out available functionality and verify syntax structure for the current version (v5.1.2) before altering database states.
```plaintext
[recon-ng][default] > help
```
Followed by a targeted query to understand workspace management syntax:
```plaintext
[recon-ng][default] > workspaces help
```

<br>

![](images/recon_ng_lab_help_menu.png)

<br>


### 3. Data Isolation (Workspace Creation)
In professional OSINT engagements, dumping target data into a default database leads to cross-contamination. To ensure strict data hygiene, a dedicated sandbox workspace named `test` was provisioned.
```plaintext
[recon-ng][default] > workspaces create test
```
<br>

![](images/recon_ng_lab_workspaces_create_test.png)

<br>

> [!IMPORTANT]
> Structuring data into unique workspaces ensures that findings for one target are cleanly segmented, making database exports accurate and preventing accidental exposure of out-of-scope assets.

**With the prompt successfully shifted to `[recon-ng][test] >`, the help menu was queried again to confirm available commands within the new context.**

 
### 4. Context Exit
 Finally, the session was cleanly terminated using the `back` command.
```plaintext
[recon-ng][test] > back
```
<br>

![](images/recon_ng_lab_test_workspace_back.png)

<br>

Because the prompt was already at the top-level context of the newly created workspace, invoking `back` effectively dropped the session completely out of the framework, returning control natively to the Kali host shell.

# Demonstrated Competencies 
This sequence highlights core baseline skills required for modern reconnaissance:
- Command-Line Fluency 
- Recon-ng Help Menu 
- Data Isolation & Hygiene 
- Environment Setup 
- Context Switching


---


**Researcher:** CypherX  
​**Date:** 2026-05-29  
​**Scope:** Framework Initialization & Workspace Isolation  
