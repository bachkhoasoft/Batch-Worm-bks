**BatchWorm**

---

# 🐛 Batchworm Malware Behavior Overview

## 🔍 Reconnaissance

* **Netstat**
  Parses `netstat` output to identify potential hosts connected on **port 445 (SMB)** before propagating.

* **ARP Table**
  Uses the cached ARP table to locate potential hosts before connecting.

* **Ping Sweep**
  Pings the entire `0/24` subnet of the current machine to find **active hosts**, then attempts logins on **port 445 (SMB)**.

---

## 🧠 Credential Dumping

* **Windows Credential Editor (WCE)**
  WCE is run in the background to **dump administrator credentials** into:

  ```
  C:\Windows\Temp\dapw
  ```

---

## 🔐 Persistence Mechanism

* **Task Scheduler**
  Persistence is established through **scheduled tasks**.

---

## 🦠 Propagation Method

* **Net Use**
  Uses captured **administrator credentials** to:

  * Map shared drives on discovered hosts
  * Copy `main.bat` and `dapw` to those hosts

* **Remote Execution via WMIC**
  Executes remotely using the same dumped credentials.

---

## ⚙️ Configuration

* **Editable Settings in `main.bat`:**

  * Target IP addresses
  * Subnet information

---

## 🏗️ Building the Worm

1. Adjust the following files:

   * `build/batchworm.SED`
   * `build/pl.SED`
     Edit values for:
   * `TargetName`
   * `SourceFiles0`

   Default working directory:

   ```
   C:\batchworm
   ```

2. Run:

   ```
   build-batchworm.bat
   ```

3. Output will be available in the `release` folder.

---

## 🧾 Indicators of Compromise (IOC)

### 🗂️ File System Artifacts

| Path                                          | Description                             |
| --------------------------------------------- | --------------------------------------- |
| `C:\Users\[user]\Appdata\Local\Temp\IPX*.TMP` | IExpress-extracted files in user temp   |
| `C:\Windows\Temp\IPX*.TMP`                    | IExpress-extracted files in system temp |
| `C:\Windows\Temp\dapw`                        | Dumped cleartext credentials            |
| `C:\Windows\Temp\main.bat`                    | Batchworm script                        |
| `C:\pwned.txt`                                | Post-infection marker                   |

### 🧬 Registry Keys

| Key                                   | Purpose                                                |
| ------------------------------------- | ------------------------------------------------------ |
| `HKLM\SOFTWARE\Microsoft\isInstalled` | Infection marker                                       |
| `HKLM\SOFTWARE\Microsoft\killswitch`  | **Killswitch**: Presence causes worm to self-terminate |

### 🕑 Scheduled Task

* `Microsoft\Windows\SoftwareProtectionPlatform\PlatformMaintenance`
  Used for persistence.

---

## 🔒 Prevention (Killswitch)

* Add registry key:

  ```
  HKLM\SOFTWARE\Microsoft\killswitch
  ```

  This causes the worm to **terminate immediately** upon execution.

---

## 🧹 Removal Instructions

* Run the script:

  ```
  remove-batchworm.bat
  ```
* If remnants remain:

  * Manually delete referenced files
  * Remove registry entries as listed in `remove-batchworm.bat`

---

Let me know if you want this saved as a downloadable `.md` file or converted into a PDF.
