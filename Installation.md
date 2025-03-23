# 🛠️ Installation Guide: Next Generation SIEM Stack

## 📌 Overview
This guide covers the step-by-step installation of the **Next Generation SIEM Stack**, including:
✅ **Server Setup** – OS installation, system hardening, and prerequisites.  
✅ **Wazuh SIEM Installation** – Log analysis, intrusion detection, and vulnerability assessment.  
✅ **Suricata IDS/IPS Deployment** – Real-time network threat detection.  
✅ **OPNsense Firewall Setup** – Network filtering and security enforcement.  
✅ **Docker Deployment (Optional)** – Running SIEM components in containers.  

---

## 1️⃣ Server Setup & Prerequisites
### **🔹 Step 1: Install Ubuntu Server 22.04**
The SIEM stack runs on **Ubuntu Server 22.04**.
1. Download the latest **Ubuntu Server ISO** from [Ubuntu Official Site](https://ubuntu.com/download/server).
2. Boot your server and follow the installation prompts.
3. Set up a static IP address using:
   ```bash
   sudo nano /etc/netplan/00-installer-config.yaml
   ```
   Example configuration:
   ```yaml
   network:
     ethernets:
       ens192:
         addresses:
           - 10.200.200.5/24
         gateway4: 10.200.200.1
         nameservers:
           addresses:
             - 8.8.8.8
             - 8.8.4.4
     version: 2
   ```
   Apply changes:
   ```bash
   sudo netplan apply
   ```

### **🔹 Step 2: Update & Secure the System**
1. Update system packages:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```
2. Install essential tools:
   ```bash
   sudo apt install curl wget vim git unzip -y
   ```
3. Set up a firewall (UFW):
   ```bash
   sudo ufw allow OpenSSH
   sudo ufw enable
   ```

---

## 2️⃣ Installing Wazuh SIEM
Wazuh is the **core SIEM** for log collection, anomaly detection, and security event analysis.
### **🔹 Step 1: Install Wazuh Manager**
1. Add the Wazuh repository:
   ```bash
   curl -sO https://packages.wazuh.com/key/GPG-KEY-WAZUH && sudo apt-key add GPG-KEY-WAZUH
   echo "deb https://packages.wazuh.com/4.x/apt/ stable main" | sudo tee /etc/apt/sources.list.d/wazuh.list
   ```
2. Install Wazuh Manager:
   ```bash
   sudo apt update && sudo apt install wazuh-manager -y
   ```
3. Start and enable the service:
   ```bash
   sudo systemctl enable --now wazuh-manager
   ```

### **🔹 Step 2: Install Wazuh Agent**
1. Install the Wazuh Agent on monitored endpoints:
   ```bash
   sudo apt install wazuh-agent -y
   ```
2. Configure the agent to connect to the Wazuh Manager:
   ```bash
   sudo nano /var/ossec/etc/ossec.conf
   ```
   Update `<address>` with your Wazuh Manager IP:
   ```xml
   <address>10.200.200.5</address>
   ```
3. Start and enable the agent:
   ```bash
   sudo systemctl enable --now wazuh-agent
   ```

---

## 3️⃣ Installing Suricata IDS/IPS
Suricata provides **network-based intrusion detection and prevention**.
### **🔹 Step 1: Install Suricata**
1. Add Suricata PPA:
   ```bash
   sudo add-apt-repository ppa:oisf/suricata-stable -y
   ```
2. Install Suricata:
   ```bash
   sudo apt update && sudo apt install suricata -y
   ```
3. Enable Suricata IPS mode:
   ```bash
   sudo nano /etc/suricata/suricata.yaml
   ```
   Change the **af-packet** section:
   ```yaml
   af-packet:
     - interface: eth0
       cluster-id: 99
       cluster-type: cluster_flow
       defrag: yes
       use-mmap: yes
   ```
   Restart Suricata:
   ```bash
   sudo systemctl restart suricata
   ```

### **🔹 Step 2: Integrate Suricata with Wazuh**
1. Configure Suricata to send logs to Wazuh:
   ```bash
   sudo nano /etc/suricata/suricata.yaml
   ```
   Add the following under `outputs:`
   ```yaml
   - eve-log:
       enabled: yes
       filetype: json
       filename: /var/log/suricata/eve.json
   ```
2. Restart Suricata:
   ```bash
   sudo systemctl restart suricata
   ```

---

## 4️⃣ Installing OPNsense Firewall
OPNsense acts as a **network security gateway** with **Suricata IDS integration**.
### **🔹 Step 1: Install OPNsense**
1. Download OPNsense ISO from [official website](https://opnsense.org/download/).
2. Install and configure basic network settings.
3. Access the web GUI at `https://<OPNsense-IP>`.

### **🔹 Step 2: Enable IDS/IPS in OPNsense**
1. Navigate to **Services > Intrusion Detection**.
2. Enable IDS and IPS mode.
3. Add Suricata rule sets (ET Open, Abuse.ch, SSL Blacklist).
4. Apply changes and restart Suricata in OPNsense.

---

## 5️⃣ Deploying SIEM with Docker (Optional)
For scalable deployments, you can run Wazuh, Suricata, and Elasticsearch in **Docker containers**.

### **🔹 Step 1: Install Docker & Docker-Compose**
1. Install Docker:
   ```bash
   curl -fsSL https://get.docker.com -o get-docker.sh && sh get-docker.sh
   ```
2. Install Docker-Compose:
   ```bash
   sudo apt install docker-compose -y
   ```

### **🔹 Step 2: Deploy Wazuh SIEM Stack**
1. Clone the Wazuh Docker repository:
   ```bash
   git clone https://github.com/wazuh/wazuh-docker.git && cd wazuh-docker
   ```
2. Start the Wazuh SIEM Stack:
   ```bash
   docker-compose up -d
   ```
3. Access the Wazuh dashboard at `https://<SIEM-IP>:5601`.

---

## 🎯 Conclusion
✅ Your **Next Generation SIEM Stack** is now deployed! 🚀  
🔹 Access Wazuh Dashboard: `https://<SIEM-IP>:5601`  
🔹 OPNsense Firewall UI: `https://<OPNsense-IP>`  
🔹 Monitor logs in `/var/log/wazuh/alerts.log`  

Need help? Check out the **[Use Cases](../UseCases/)** section for hands-on security monitoring examples!
