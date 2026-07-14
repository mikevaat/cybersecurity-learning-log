# Wazuh SIEM Lab Documentation

This report contains clean, step-by-step documentation for my wazuh setup lab based off of what was taught by my tutor

This lab was tested in a virtual environment using Tailscale for connectivity. 

## Part 1: Wazuh Configuration Steps

### Prerequisites
- A fresh Ubuntu/Kali VM for the **Wazuh manager** (server).
- A second Ubuntu VM (or the “Maize Bank” host) for the **Wazuh agent** (endpoint).
- Tailscale installed on both machines for easy private IP access.

### Installing the Wazuh Manager

1. **Update your package list:**
   ```bash
   sudo apt update
   sudo apt install curl
   curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh && sudo bash ./wazuh-install.sh -a
2. **Once installation finishes, you will see a summary with your admin credentials:**
        *User: admin
         Password: 5Apuq+M+mT3b1Q1ufvDYF4G2W6pCN16?* for an example
3. **Disable the Wazuh repository so it doesn’t interfere with future updates:**
     ```bash
     sudo -i
    sed -i 's/^deb /#deb /' /etc/apt/sources.list.d/wazuh.list
    apt update
4. **Access the Wazuh dashboard:**
   Open your browser and go to https://<your-tailscale-ip>
    Log in with admin and the password from step *2*.
5. **After every reboot of the manager VM, restart the core services:**
    ```bash
    sudo systemctl restart wazuh-manager
   sudo systemctl restart wazuh-indexer
   sudo systemctl restart wazuh-dashboard
### Installing the Wazuh Agent on the endpoint

1. **In the Wazuh dashboard, go to Agents → Deploy new agent.**
     - Choose your endpoint architecture (usually DEB amd64 for Ubuntu).
     - Enter the Wazuh server’s Tailscale IP address and toggle Remember server address.
     - Give the agent a clear, unique name (e.g., Maize-Bank). This prevents auto-detection issues.
  



       
  ![Wazuh_Agent_On_The_Endpoint](images/hidden-processes-class-hands-on.jpg)


















       
2. **Start and enable the agent:**
   ```bash
   sudo systemctl daemon-reload
   sudo systemctl enable wazuh-agent
   sudo systemctl start wazuh-agent
   
3. You should see a confirmation that the symlink was created.


##### And that's exactly how to setup a wazuh server/manager and connect it to agent on their endpoints.
