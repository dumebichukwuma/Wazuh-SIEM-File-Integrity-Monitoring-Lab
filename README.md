# Wazuh SIEM File Integrity Monitoring Lab

# Phase 1: Setting up the Wazuh Manager

The easiest way to start is by using the official Wazuh OVA (Open Virtual Appliance).

## 1. Download the OVA
Go to the Wazuh documentation and download the Virtual Machine image.

## 2. Import to VirtualBox
- Open **VirtualBox** → **File** → **Import Appliance**
- Select the `.ova` file you downloaded

### Settings Adjustment
Ensure the VM has at least:
- **4GB RAM**
- **2 CPUs**

> Wazuh is resource-intensive.

## 3. Network Configuration (Crucial)

To allow the Manager and Windows VM to communicate:

- Go to:
  - **Wazuh VM Settings** → **Network**
- Change **Attached to** to:
  - **Bridged Adapter** (puts it on your home network)
  - OR
  - **Host-Only Adapter** (for a private lab)
    <img width="940" height="495" alt="image" src="https://github.com/user-attachments/assets/64c0d699-c331-4b94-aeac-17d0b09df9f2" />
    <img width="940" height="577" alt="image" src="https://github.com/user-attachments/assets/aeb45ec6-942a-428d-a64b-f78cc4b96e3b" />
    <img width="940" height="568" alt="image" src="https://github.com/user-attachments/assets/290953bf-1485-4ebd-9ae7-03a4117e4e66" />
    
## 4. Get the Manager IP

- Start the VM
- Login with:

```text
Username: wazuh-user
Password: wazuh
```
<img width="940" height="494" alt="image" src="https://github.com/user-attachments/assets/9282ec9c-7525-4398-bcb9-84b3b3d22cc9" />
<img width="940" height="708" alt="image" src="https://github.com/user-attachments/assets/deb7470f-9ec3-4966-82f0-b70b1eda3b09" />


- Run the command:

```bash
ip a
```

- Note down the IP address (example: `192.168.1.50`)

---
<img width="940" height="301" alt="image" src="https://github.com/user-attachments/assets/ae4a4d1e-8de6-41ad-b04f-dc7d5850c5ef" />



# Phase 2: Deploying the Windows 10 Agent

Now you need to tell your Windows 10 VM to send its logs to the Manager.

## 1. Access the Dashboard

On your host computer's browser, go to:

```text
https://<WAZUH_MANAGER_IP>
```

Login with:

```text
Username: admin
Password: admin
```
<img width="940" height="458" alt="image" src="https://github.com/user-attachments/assets/f745e8d7-f4ff-4787-9a9c-9e11ef087e03" />
<img width="940" height="481" alt="image" src="https://github.com/user-attachments/assets/cfb6a269-f816-4416-a1b7-f2cd505d650c" />

## 2. Generate the Agent Command

- Click:
  - **Wazuh** → **Agents** → **Deploy new agent**
    <img width="940" height="588" alt="image" src="https://github.com/user-attachments/assets/efb888a2-5ccb-4b94-96d3-0aec79f3c1fc" />

- Select:
  - **Windows** as the OS
    <img width="940" height="586" alt="image" src="https://github.com/user-attachments/assets/cabcba73-1350-4d17-8422-e2097407994f" />

- Enter the Wazuh Manager IP you noted earlier
  <img width="940" height="512" alt="image" src="https://github.com/user-attachments/assets/fa832ceb-5779-4d3d-91ed-8f9759853097" />

## 3. Install on Windows 10

- Copy the PowerShell command provided by the dashboard
  <img width="940" height="482" alt="image" src="https://github.com/user-attachments/assets/5d005308-9270-4148-85d1-0abec03c0cfa" />

- On your Windows 10 VM:
  - Open **PowerShell as Administrator**

  - Paste the command
    <img width="940" height="667" alt="image" src="https://github.com/user-attachments/assets/cd513c6c-a2b6-42e5-9d75-1255f2908bfa" />

- Start the service by running:
  <img width="940" height="663" alt="image" src="https://github.com/user-attachments/assets/1d58a673-1100-4dd3-8b60-67d9a6e6df1e" />


```powershell
Start-Service -Name wazuh
```

---

# Phase 3: Application - File Integrity Monitoring (FIM)

FIM alerts you if a sensitive file is modified, deleted, or created.

## 1. Configure the Agent

On your Windows 10 VM, open the configuration file:

```text
C:\Program Files (x86)\ossec-agent\ossec.conf
```
<img width="940" height="591" alt="image" src="https://github.com/user-attachments/assets/db072bc6-ad5a-4643-bce3-9ee7403bbe97" />

> Use Notepad as Administrator.

<img width="940" height="592" alt="image" src="https://github.com/user-attachments/assets/dc43aebd-fed7-4a1b-b61d-a4982435e37c" />

- Locate the `<syscheck>` section
- Add this line to monitor a specific folder (example: a "Secret" folder on your desktop):

```xml
<directories realtime="yes" check_all="yes">C:\Users\YourUser\Desktop\Secret</directories>
```
<img width="940" height="582" alt="image" src="https://github.com/user-attachments/assets/cd114e62-fe75-4641-82bc-63a620e07cf0" />


## 2. Restart the Agent

Run:

```powershell
Restart-Service -Name wazuh
```
<img width="940" height="212" alt="image" src="https://github.com/user-attachments/assets/0a07fa8c-bc91-4ab8-81e2-2dcdacbabbbe" />

## 3. Test It

- Create or delete a file inside the **Secret** folder
  <img width="940" height="676" alt="image" src="https://github.com/user-attachments/assets/7db4f26e-b7db-4b4b-9248-15f6e62128c9" />


## 4. View Results

Go to:
- **Wazuh Dashboard** → **Security Events**
- OR
- **File Integrity Monitoring**

You will see an alert showing exactly what changed.
<img width="940" height="577" alt="image" src="https://github.com/user-attachments/assets/d4d6df62-9748-4368-9807-bff804c9aa89" />
<img width="940" height="565" alt="image" src="https://github.com/user-attachments/assets/1cad09c3-ff7c-4e76-b44b-938bf6e37964" />


