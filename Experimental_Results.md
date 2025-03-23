# 📊 Security Analysis & Reporting

## 🔍 Overview
Security analysis and reporting are essential for evaluating the **efficiency of the SIEM stack**. This section details how to assess detection accuracy, response times, and overall system performance using **Wazuh, Suricata, and other security tools**.

✅ **Key Metrics for Evaluation:**
- **Detection Accuracy** – False positives vs. true positives.
- **Incident Response Time** – Time taken to detect & mitigate threats.
- **System Performance** – Log processing speed and resource utilization.
- **Threat Intelligence Mapping** – Correlation with MITRE ATT&CK framework.

---

## 1️⃣ Evaluating SIEM Detection Accuracy
### **🔹 Step 1: Generate Controlled Security Events**
To assess the system’s detection efficiency, simulate security events:
1. **File Integrity Change:**
   ```bash
   echo "Unauthorized Change" >> /etc/secure_file
   ```
2. **SSH Brute-Force Attack Simulation:**
   ```bash
   hydra -l admin -P /usr/share/wordlists/rockyou.txt ssh://<TARGET-IP>
   ```
3. **Network Intrusion Attempt:**
   ```bash
   nmap -sS -p 22,80,443 <TARGET-IP>
   ```

### **🔹 Step 2: Analyze Detection Accuracy**
1. Review alerts in Wazuh:
   ```bash
   tail -f /var/ossec/logs/alerts/alerts.json | jq '.'
   ```
2. Compare detection logs with simulated attacks.
3. Record **false positives** and **missed detections**.

---

## 2️⃣ Measuring Incident Response Efficiency
### **🔹 Step 1: Track Alert Generation Time**
1. Run an attack simulation (e.g., SSH brute-force attack).
2. Note the **timestamp** when the attack starts.
3. Check the **timestamp** in the Wazuh alerts log to calculate delay:
   ```bash
   grep "SSH Brute-Force Attack Detected" /var/ossec/logs/alerts/alerts.json
   ```

### **🔹 Step 2: Evaluate Automated Response Execution Time**
1. Trigger an **active response rule** (e.g., blocking an IP):
   ```bash
   iptables -L -n | grep <ATTACKER-IP>
   ```
2. Measure the time taken for automatic mitigation.

---

## 3️⃣ System Performance & Log Processing
### **🔹 Step 1: Check Wazuh Log Processing Speed**
1. Use `journalctl` to monitor Wazuh performance logs:
   ```bash
   sudo journalctl -u wazuh-manager --since "10 minutes ago"
   ```
2. Measure how fast logs are ingested and analyzed.

### **🔹 Step 2: Monitor CPU & Memory Usage**
1. Check Wazuh and Suricata resource utilization:
   ```bash
   top -p $(pgrep -d',' wazuh-manager suricata)
   ```
2. Analyze spikes in CPU and memory during heavy log processing.

---

## 4️⃣ Threat Intelligence & MITRE ATT&CK Mapping
### **🔹 Step 1: Correlate Detected Threats with ATT&CK Framework**
1. Open the **Wazuh Dashboard** (`https://<SIEM-IP>:5601`).
2. Navigate to **Security Events > MITRE ATT&CK**.
3. View **mapped attack techniques** for each detected threat.

### **🔹 Step 2: Generate Security Reports**
1. Export detected threats in JSON format:
   ```bash
   cat /var/ossec/logs/alerts/alerts.json | jq '.' > security_report.json
   ```
2. Use Kibana or Grafana dashboards for **graphical analysis**.

---

## 🎯 Summary
🚀 **Your SIEM performance has been evaluated!**
- **Analyze detection accuracy** to optimize security rules.
- **Measure incident response time** to improve threat mitigation.
- **Monitor system performance** to enhance efficiency.
- **Map threats with MITRE ATT&CK** for actionable insights.

🔹 **Next Steps:** Optimize your **SIEM Stack** based on analysis findings!
