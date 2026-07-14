  ## **Identifying hidden processes on a system using the wazuh solution**

This documentation details the process of identifying processes hidden by the Diamorphine LKM (Loadable Kernel Module) rootkit using Wazuh’s rootcheck engine.

# **Engine Optimization**

​1. To enable real-time detection for laboratory testing, the scan frequency is modified from the default 12-hour window to a 2-minute (120 seconds) interval. In my personal lab, I decide to tweak it and use a 30 seconds interval.

2. Open the configuration file: 
  ```bash
        sudo nano /var/ossec/etc/ossec.conf
```
3. The <rootcheck> file is the updated using the following parameters 
```bash
<rootcheck>
  <disabled>no</disabled>
  <check_pids>yes</check_pids>
  <check_ports>yes</check_ports>
  <check_sys>yes</check_sys>
  <check_dev>yes</check_dev>
  <frequency>120</frequency>
</rootcheck>
```
# **Attack Simulation (Diamorphine)**

1. ​The Diamorphine rootkit is utilized here to simulate a stealth attack by intercepting system calls and hiding specific PIDs from the process table.
```bash
Install build dependencies
apt -y install gcc git

[Internal Step] Clone and compile the Diamorphine module via 'make'
[Internal Step] Load the module: insmod diamorphine.ko
```
2. Verification of Hidden Processes


​The following sequence demonstrates the transition of a process from a visible state to a hidden state using kernel signals.
```bash
1. Identify the Target PID (using rsyslogd as an example)
ps auxw | grep rsyslogd | grep -v grep

2. Trigger Stealth Mode
# Send signal 31 to toggle visibility for the specific PID
kill -31 <TARGET_PID>

3. Confirm Stealth Status
# This command should now return an empty output
ps auxw | grep rsyslogd | grep -v grep
```
3. Incident Visualization


​After the process is hidden from the standard ps command, the Wazuh rootcheck engine identifies the discrepancy between the /proc filesystem and kernel process list.

# Conclusion
The resulting alert confirms the detection of a hidden process, categorized under Rule ID: 510 (Host-based IDS alert).
