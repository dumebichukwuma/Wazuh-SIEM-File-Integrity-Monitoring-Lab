# Wazuh SIEM File Integrity Monitoring Lab

## Phase 1: Setting up the Wazuh Manager[cite: 1]
The easiest way to start is by using the official Wazuh OVA (Open Virtual Appliance)[cite: 1].

1. **Download the OVA:** Go to the Wazuh documentation and download the Virtual Machine image[cite: 1].
2. **Import to VirtualBox:**[cite: 1]
    * Open VirtualBox → **File** → **Import Appliance**[cite: 1].
    * Select the `.ova` file you downloaded[cite: 1].
3. **Settings Adjustment:** Ensure the VM has at least **4GB RAM** and **2 CPUs**[cite: 1].
4. **Network Configuration (Crucial):**[cite: 1]
    * Go to the Wazuh VM **Settings** → **Network**[cite: 1].
    * Change "Attached to" to **Bridged Adapter** or **Host-Only Adapter**[cite: 1].
5. **Get the Manager IP:**[cite: 1]
    * Start the VM and login with `wazuh-user` / `wazuh`[cite: 1].
    * Run the command `ip a` and note the IP (e.g., `10.34.138.63`)[cite: 1].

## Phase 2: Deploying the Windows 10 Agent[cite: 1]
1. **Access the Dashboard:** Go to `https://<WAZUH_MANAGER_IP>` in your browser[cite: 1].
2. **Login:** Use `admin` / `admin`[cite: 1].
3. **Generate Command:**[cite: 1]
    * Click **Wazuh** → **Agents** → **Deploy new agent**[cite: 1].
    * Select **Windows** and enter your Manager IP[cite: 1].
4. **Install:** Copy the PowerShell command and run it as **Administrator** on the Windows 10 VM[cite: 1].
5. **Start Agent:** Run `NET START Wazuh` in PowerShell[cite: 1].

## Phase 3: Application - File Integrity Monitoring (FIM)[cite: 1]
1. **Configure Agent:**[cite: 1]
    * Open `C:\Program Files (x86)\ossec-agent\ossec.conf` as Admin[cite: 1].
    * Add this line to the `<syscheck>` section:[cite: 1]
    ```xml
    <directories realtime="yes" check_all="yes">C:\Users\YourUser\Desktop\Secret</directories>
    ```
2. **Restart:** Run `Restart-Service -Name wazuh` in PowerShell[cite: 1].
3. **Test:** Create or delete a file in the "Secret" folder[cite: 1].
4. **View Results:** Check the **Wazuh Dashboard** → **Security Events**[cite: 1].
