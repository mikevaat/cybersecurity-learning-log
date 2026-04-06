# Incident Response 1

## Summary
**Incident Response (IR)** is a structured, organized approach to handling cybersecurity threats. It helps organizations quickly identify, contain, eradicate, and recover from security incidents while minimizing damage, reducing downtime, and preventing future attacks.  


## What is Incident Response?
- A structured approach to handling cybersecurity threats  
- Helps identify, contain, and manage incidents  
- Ensures quick and organized response  

## Why Incident Response Matters
- Minimizes damage  
- Reduces downtime  
- Helps prevent repeat incidents  
- Protects business reputation  

## The Incident Response Lifecycle
The standard process follows these five phases:

1. **Preparation**  
2. **Detection & Analysis**  
3. **Containment**  
4. **Eradication & Recovery**  
5. **Lessons Learned**

### 1. Preparation
- Build response plans and playbooks  
- Assign roles and responsibilities  
- Practice using drills and tabletop exercises  
- Ensure tools are ready (SIEM, EDR, SOAR)

### 2. Detection & Analysis
- Identify suspicious activity using SOC tools  
- Validate alerts (real incident or false alarm?)  
- Collect supporting evidence (logs, alerts, endpoint data)

**Alert Classification (Live Class)**  
![Classification of Incident Alerts](/classification-alerts.jpg)

- **True Positive (TP)**: The alert was genuine and a real security incident occurred. The detection system correctly identified a threat.  
- **False Positive (FP)**: The alert was generated, but no real security incident occurred (a benign event was flagged as malicious). This is a common issue in SOCs and requires analyst time to investigate and close.

### 3. Containment
- **Short-term**: Stop the spread quickly  
- **Long-term**: Remove attacker access permanently  

**Examples**:
- Disconnect infected systems  
- Block malicious IPs/domains  
- Disable compromised accounts

### 4. Eradication & Recovery
- Remove malware or malicious access  
- Patch vulnerabilities  
- Restore normal operations  
- Monitor system closely

### 5. Lessons Learned
- Review what happened  
- Identify gaps  
- Update processes and tools

## Escalation & Communication (Live Class)
![Escalation & Communication](/escalation-communication.jpg)

**When to Escalate**:
- Incident is high-risk  
- Systems show ongoing compromise  
- Impact affects business operations  

**Internal and External Communication**:
- **Internal**: SOC team, IT, leadership  
- **External** (if needed): vendors, regulators, authorities

## Evidence Collection
**Key examples**:
- Logs  
- Network data  
- Malware samples (handled safely in a sandbox)  
- User activity history

---

