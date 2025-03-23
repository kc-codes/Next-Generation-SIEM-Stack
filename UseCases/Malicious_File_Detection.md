# 🦠 Malicious File Detection with VirusTotal

## 🔍 Overview
Malicious files can compromise system security by executing harmful payloads. **Wazuh SIEM** integrates with the **VirusTotal API** to automatically scan suspicious files, detect malware, and trigger security alerts for incident response.

✅ **Key Features:**
- Automates file scanning using the **VirusTotal API**.
- Detects and quarantines **malicious files**.
- Generates alerts for **malware infections**.
- Provides actionable threat intelligence for remediation.

---

## 1️⃣ Configuring VirusTotal Integration
### **🔹 Step 1: Obtain a VirusTotal API Key**
1. Create a free account at [VirusTotal](https://www.virustotal.com/).
2. Navigate to **API Key Settings** and copy your API key.

### **🔹 Step 2: Configure Wazuh for VirusTotal**
1. Open the Wazuh configuration file:
   ```bash
   sudo nano /var/ossec/etc/ossec.conf
   ```
2. Add the following under `<wodle name="virustotal">`:
   ```xml
   <wodle name="virustotal">
     <enabled>yes</enabled>
     <api_key>Your-VirusTotal-API-Key</api_key>
     <group>malware</group>
   </wodle>
   ```
3. Save and restart Wazuh:
   ```bash
   sudo systemctl restart wazuh-manager
   ```

---

## 2️⃣ Automating File Scanning
### **🔹 Step 1: Monitor Suspicious Files**
1. Open Wazuh’s configuration file:
   ```bash
   sudo nano /var/ossec/etc/ossec.conf
   ```
2. Add directories to be monitored for suspicious file activity:
   ```xml
   <syscheck>
     <directories check_all="yes">/home,/var,/tmp</directories>
   </syscheck>
   ```
3. Save and restart Wazuh:
   ```bash
   sudo systemctl restart wazuh-manager
   ```

---

## 3️⃣ Testing & Validation
### **🔹 Step 1: Simulate a Malware File**
1. Download a test file from EICAR (safe malware test sample):
   ```bash
   curl -o /tmp/eicar.com.txt https://secure.eicar.org/eicar.com.txt
   ```
2. Check Wazuh alerts for the detected file:
   ```bash
   tail -f /var/ossec/logs/alerts/alerts.json | jq '.'
   ```

Expected output:
```json
{
  "rule": {
    "id": "100050",
    "description": "Malware detected via VirusTotal"
  },
  "file": "/tmp/eicar.com.txt",
  "alert_level": 10
}
```

---

## 4️⃣ Automated Response: Quarantining Malicious Files
### **🔹 Step 1: Enable Active Response for Malware**
1. Open Wazuh’s configuration file:
   ```bash
   sudo nano /var/ossec/etc/ossec.conf
   ```
2. Add the following command to quarantine infected files:
   ```xml
   <command>
     <name>quarantine_malware</name>
     <executable>mv</executable>
     <extra_args>$FILENAME /var/quarantine/</extra_args>
   </command>
   ```
3. Save and restart Wazuh:
   ```bash
   sudo systemctl restart wazuh-manager
   ```

---

## 🎯 Summary
🚀 **Malicious File Detection with VirusTotal is now active!**
- **Detect malware in real time** using VirusTotal API.
- **Monitor and scan suspicious files** automatically.
- **Quarantine infected files** before they cause harm.

🔹 **Next Steps:** Explore **[Security Awareness Module](Security_Awareness_Module.md)** to analyze detected threats!

🔹 **Next Steps:** Explore **[Security Analysis & Reporting](Experimental_Results.md)** to analyze detected threats!
