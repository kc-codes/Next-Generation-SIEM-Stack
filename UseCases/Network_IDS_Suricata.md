# 🌐 Network Intrusion Detection with Suricata

## 🔍 Overview
**Suricata** is a high-performance **Network Intrusion Detection System (NIDS)** that monitors network traffic for **malicious activity, port scans, and known attack patterns**. Integrated with **Wazuh SIEM**, it provides **real-time threat detection and alerting**.

✅ **Key Features:**
- Deep Packet Inspection (DPI) for protocol-based attack detection.
- Custom rule creation for network security policies.
- Integration with **MITRE ATT&CK** for threat intelligence mapping.
- Real-time alerting in **Wazuh and Kibana dashboards**.

---

## 1️⃣ Installing & Configuring Suricata
### **🔹 Step 1: Install Suricata**
1. Add Suricata’s official repository:
   ```bash
   sudo add-apt-repository ppa:oisf/suricata-stable -y
   ```
2. Install Suricata:
   ```bash
   sudo apt update && sudo apt install suricata -y
   ```
3. Verify installation:
   ```bash
   suricata --build-info
   ```

### **🔹 Step 2: Configure Suricata for IDS Mode**
1. Open Suricata’s configuration file:
   ```bash
   sudo nano /etc/suricata/suricata.yaml
   ```
2. Locate the **af-packet** section and ensure IDS mode is enabled:
   ```yaml
   af-packet:
     - interface: eth0
       cluster-id: 99
       cluster-type: cluster_flow
       defrag: yes
       use-mmap: yes
   ```
3. Save and restart Suricata:
   ```bash
   sudo systemctl restart suricata
   ```

---

## 2️⃣ Integrating Suricata with Wazuh
### **🔹 Step 1: Enable Suricata Logging**
1. Edit Suricata’s `eve-log` settings in `/etc/suricata/suricata.yaml`:
   ```yaml
   outputs:
     - eve-log:
         enabled: yes
         filetype: json
         filename: /var/log/suricata/eve.json
   ```
2. Restart Suricata:
   ```bash
   sudo systemctl restart suricata
   ```

### **🔹 Step 2: Configure Wazuh to Monitor Suricata Logs**
1. Open Wazuh’s configuration file:
   ```bash
   sudo nano /var/ossec/etc/ossec.conf
   ```
2. Add the following under `<localfile>`:
   ```xml
   <localfile>
     <log_format>json</log_format>
     <location>/var/log/suricata/eve.json</location>
   </localfile>
   ```
3. Restart Wazuh:
   ```bash
   sudo systemctl restart wazuh-manager
   ```

---

## 3️⃣ Attack Simulation & Detection
### **🔹 Step 1: Simulate a Port Scan Attack**
Run an **Nmap scan** to trigger Suricata alerts:
```bash
nmap -sS -p 22,80,443 <TARGET-IP>
```

### **🔹 Step 2: Check Wazuh Alerts**
Monitor real-time logs:
```bash
tail -f /var/ossec/logs/alerts/alerts.json | jq '.'
```
Example alert output:
```json
{
  "rule": {
    "id": "2020101",
    "description": "Nmap Scan Detected"
  },
  "source": "192.168.1.100",
  "alert_level": 8
}
```

---

## 4️⃣ Automating Intrusion Prevention (IPS Mode)
To **block malicious IPs dynamically**, enable Suricata’s **IPS mode**:
1. Edit `/etc/suricata/suricata.yaml`:
   ```yaml
   outputs:
     - eve-log:
         enabled: yes
         filetype: json
         filename: /var/log/suricata/eve.json
     - drop:
         enabled: yes
   ```
2. Restart Suricata:
   ```bash
   sudo systemctl restart suricata
   ```

---

## 🎯 Summary
🚀 **Network Intrusion Detection (NIDS) is now active!**
- **Detect & log network-based attacks** in real time.
- **Automate blocking of malicious IPs** using IPS mode.
- **View alerts in Wazuh & Suricata dashboards**.

🔹 **Next Steps:** Explore **[Vulnerability Assessment](Vulnerability_Assessment.md)** to detect outdated software & security flaws!
