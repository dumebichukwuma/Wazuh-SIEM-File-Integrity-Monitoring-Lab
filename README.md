# Wazuh SIEM File Integrity Monitoring Lab
Phase 1: Setting up the Wazuh Manager
The easiest way to start is by using the official Wazuh OVA (Open Virtual Appliance).  

Download the OVA: Go to the Wazuh documentation and download the Virtual Machine image.  

Import to VirtualBox:

  

Open VirtualBox → File → Import Appliance.  

Select the .ova file you downloaded.  

Settings Adjustment: Ensure the VM has at least 4GB RAM and 2 CPUs as Wazuh is resource-intensive.  

Network Configuration (Crucial):

  

Go to the Wazuh VM Settings → Network.  

Change "Attached to" to Bridged Adapter (to put it on your home network) or Host-Only Adapter (for a private lab).  

Get the Manager IP:

  

Start the VM and login with wazuh-user / wazuh.  

Run the command ip a and note down the IP address (e.g., 10.34.138.63).  

Phase 2: Deploying the Windows 10 Agent
Now you need to tell your Windows 10 VM to send its logs to the Manager.  

Access the Dashboard: On your host computer's browser, go to https://<WAZUH_MANAGER_IP>.  

Login: Use the default credentials admin / admin.  

Generate the Agent Command:

  

Click Wazuh → Agents → Deploy new agent.  

Select Windows as the OS.  

Enter the Wazuh Manager IP you noted earlier.  

Install on Windows 10:

  

Copy the PowerShell command provided by the dashboard.  

On your Windows 10 VM, open PowerShell as Administrator and paste the command.  

Start the Agent: Run the following command in PowerShell:  

PowerShell
NET START Wazuh
Phase 3: Application - File Integrity Monitoring (FIM)
FIM alerts you if a sensitive file is modified, deleted, or created.  

Configure the Agent:

  

On your Windows 10 VM, open C:\Program Files (x86)\ossec-agent\ossec.conf using Notepad as Administrator.  

Locate the <syscheck> section and add a line to monitor your folder:  

XML
<directories realtime="yes" check_all="yes">C:\Users\YourUser\Desktop\Secret</directories>
Restart the Agent: Run Restart-Service -Name wazuh in PowerShell.  

Test it: Create or delete a file inside that "Secret" folder.  

View Results: Go to the Wazuh Dashboard → Security Events or File Integrity Monitoring tab to see the alert.
