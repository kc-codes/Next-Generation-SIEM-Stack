# ⚠️ Malicious Command Execution Detection

## 🔍 Overview
Detecting **unauthorized command executions** is crucial for preventing **privilege escalation, backdoor access, and system exploitation**. This module uses **Wazuh SIEM** to monitor executed commands and generate alerts for **suspicious activities**.

✅ **Key Features:**
- Monitors **high-risk system commands** (e.g., sudo, chmod, netcat).
- Uses **regular expressions** to detect malicious patterns.
- Generates **real-time alerts** for security teams.
- Enables **automated countermeasures** against detected threats.

---

## 1️⃣ Enabling Command Execution Monitoring
### **🔹 Step 1: Configure Wazuh to Monitor Shell Commands**
1. Open the Wazuh configuration file:
   ```bash
   sudo nano /var/ossec/etc/ossec.conf
   ```
2. Add the following under `<localfile>` to monitor shell history logs:
   ```xml
   <localfile>
     <log_format>syslog</log_format>
     <location>/home/*/.bash_history</location>
   </localfile>
   ```
3. Save the file and restart Wazuh:
   ```bash
   sudo systemctl restart wazuh-manager
   ```

---

## 2️⃣ Creating Custom Detection Rules
### **🔹 Step 1: Define Suspicious Command Patterns**
1. Open the Wazuh rules file:
   ```bash
   sudo nano /var/ossec/rules/local_rules.xml
   ```
2. Add detection rules for dangerous commands:
   ```xml
   <group name="command_execution"> 
     <rule id="100020" level="8"> 
       <decoded_as>syslog</decoded_as>
       <match>sudo su</match>
       <description>Potential Privilege Escalation Detected</description>
     </rule> 
     <rule id="100021" level="9"> 
       <decoded_as>syslog</decoded_as>
       <match>nc -e /bin/sh</match>
       <description>Reverse Shell Execution Attempt</description>
     </rule> 
   </group>
   ```
3. Save and restart Wazuh:
   ```bash
   sudo systemctl restart wazuh-manager
   ```

---

## 3️⃣ Testing & Validation
### **🔹 Step 1: Simulate a Suspicious Command Execution**
1. Run the following **test command**:
   ```bash
   sudo su
   ```
2. Monitor alerts in Wazuh:
   ```bash
   tail -f /var/ossec/logs/alerts/alerts.json | jq '.'
   ```
Expected alert output:
```json
{
  "rule": {
    "id": "100020",
    "description": "Potential Privilege Escalation Detected"
  },
  "alert_level": 8
}
```

---

## 4️⃣ Automating Response & Mitigation
### **🔹 Step 1: Block Unauthorized Users**
1. Open Wazuh’s configuration file:
   ```bash
   sudo nano /var/ossec/etc/ossec.conf
   ```
2. Add an **active response** rule:
   ```xml
   <command>
     <name>disable_user</name>
     <executable>usermod</executable>
     <extra_args>-L $USERNAME</extra_args>
   </command>
   ```
3. Save and restart Wazuh:
   ```bash
   sudo systemctl restart wazuh-manager
   ```

---

## 🎯 Summary
🚀 **Malicious Command Execution Detection is now active!**
- **Detect unauthorized commands** before exploitation.
- **Automate user restrictions** for high-risk activities.
- **View alerts in Wazuh Dashboard** for forensic analysis.

🔹 **Next Steps:** Explore **[SSH Brute-Force Attack Prevention](SSH_Brute_Force_Prevention.md)** to block unauthorized login attempts!
