# 🔐 SSH Brute-Force Attack Detection & Prevention

## 🔍 Overview
**SSH brute-force attacks** occur when an attacker repeatedly attempts to gain access by guessing passwords. This module detects and prevents such attacks using **Wazuh SIEM and firewall automation**.

✅ **Key Features:**
- Monitors SSH logs for **failed login attempts**.
- Detects brute-force attempts **based on thresholds**.
- Automatically **blocks malicious IPs** using firewall rules.
- Generates **real-time security alerts**.

---

## 1️⃣ Configuring Wazuh for SSH Log Monitoring
### **🔹 Step 1: Enable SSH Log Collection**
1. Open the Wazuh configuration file:
   ```bash
   sudo nano /var/ossec/etc/ossec.conf
   ```
2. Add the following under `<localfile>`:
   ```xml
   <localfile>
     <log_format>syslog</log_format>
     <location>/var/log/auth.log</location>
   </localfile>
   ```
3. Save and restart Wazuh:
   ```bash
   sudo systemctl restart wazuh-manager
   ```

---

## 2️⃣ Creating Detection Rules for SSH Attacks
### **🔹 Step 1: Define Custom Brute-Force Rules**
1. Open the Wazuh rules file:
   ```bash
   sudo nano /var/ossec/rules/local_rules.xml
   ```
2. Add the following rules:
   ```xml
   <group name="ssh_bruteforce"> 
     <rule id="100030" level="8"> 
       <decoded_as>syslog</decoded_as>
       <match>Failed password for</match>
       <description>SSH Brute-Force Attempt Detected</description>
     </rule> 
     <rule id="100031" level="10"> 
       <if_sid>100030</if_sid>
       <frequency>5</frequency>
       <description>Multiple SSH Login Failures - Possible Attack</description>
     </rule> 
   </group>
   ```
3. Save and restart Wazuh:
   ```bash
   sudo systemctl restart wazuh-manager
   ```

---

## 3️⃣ Attack Simulation & Detection
### **🔹 Step 1: Simulate a Brute-Force Attack**
Run an SSH brute-force attempt using **Hydra**:
```bash
hydra -l root -P passwords.txt ssh://<TARGET-IP>
```

### **🔹 Step 2: Verify Wazuh Alerts**
Monitor real-time logs:
```bash
tail -f /var/ossec/logs/alerts/alerts.json | jq '.'
```
Example alert output:
```json
{
  "rule": {
    "id": "100031",
    "description": "Multiple SSH Login Failures - Possible Attack"
  },
  "source": "192.168.1.200",
  "alert_level": 10
}
```

---

## 4️⃣ Blocking Attacks with Firewall Rules
### **🔹 Step 1: Automate IP Blocking**
1. Open Wazuh’s configuration file:
   ```bash
   sudo nano /var/ossec/etc/ossec.conf
   ```
2. Add an **active response** command:
   ```xml
   <command>
     <name>block_ip</name>
     <executable>iptables</executable>
     <extra_args>-A INPUT -s $SOURCEIP -j DROP</extra_args>
   </command>
   ```
3. Save and restart Wazuh:
   ```bash
   sudo systemctl restart wazuh-manager
   ```

---

## 🎯 Summary
🚀 **SSH Brute-Force Detection & Prevention is now active!**
- **Detect repeated SSH login failures** in real time.
- **Automatically block attacking IPs** using firewall rules.
- **View all security alerts** in the Wazuh dashboard.

🔹 **Next Steps:** Explore **[Malicious File Detection](Malicious_File_Detection.md)** to scan and quarantine malware-infected files!