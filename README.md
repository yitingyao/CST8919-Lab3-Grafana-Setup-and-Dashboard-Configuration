# CST8919 Lab3 :Grafana Setup and Dashboard Configuration
## Lab Overview
In this lab, I installed and configured Grafana on an Ubuntu VM, then connected it to Azure Monitor using a managed identity. I learned how to gather and visualize real-time metrics—such as CPU usage, memory utilization, and network traffic—by creating a custom data dashboard. The end result is a fully functioning performance monitoring setup for my Ubuntu virtual machine.
## Task 1: Prepare Ubuntu Server
### Connect to the Server via SSH
```sh
ssh -i "C:\Users\Yitin\OneDrive\CloudDevOpsTina\DevSecOps\Lab03\grafanavmtina_key.pem" azureuser@130.107.138.0
```

### Update and Upgrade Packages
```sh
sudo apt-get update && sudo apt-get upgrade -y
```

### Install Required Dependencies
```sh
sudo apt-get install -y apt-transport-https software-properties-common wget
```
![SSH](1a.png)
![Preparing Server](1b.png)
![Server Up and Running](1c.png)
---

## Task 2: Install Grafana
### Add Grafana's Official APT Repository
```sh
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
echo "deb https://packages.grafana.com/oss/deb stable main" | sudo tee /etc/apt/sources.list.d/grafana.list
```

### Install Grafana
```sh
sudo apt-get update
sudo apt-get install -y grafana
```

### Start and Enable Grafana Service
```sh
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
```

### Verify the Service is Running
```sh
sudo systemctl status grafana-server
```
![alt text](2a.png)
![alt text](2c.png)

### Enable Port 3000 on Azure
![Port3000](<2c2 (enable Port 3000).png>)

### Login to Grafana UI with Username and Password as "admin"
![LoggedIn](2d.png)

---

## Task 3: Connect Grafana to Azure Monitor
1. **Enable Managed Identity in your Grafana VM:**
   - Assign a **System-assigned Managed Identity** to the VM in Azure.
   - I assigned the VM’s Managed Identity **Monitoring Reader Role** on Azure Monitor and **Reader Role** on my Subscription.
  ![MonitoringReaderRole](3a.png)

1. **Enable Managed Identity authentication in Grafana:**
   - Edit the Grafana configuration file:
     ```sh
     sudo nano /etc/grafana/grafana.ini
     ```
   - Locate the `[azure]` section in the configuration file and configure authentication using Managed Identity by inserting line **managed_identity_enabled = true** into the file.  
  ![GrafanaConfigFile](3b.png)

1. **Restart the Grafana server:**
   ```sh
   sudo systemctl stop grafana-server
   sudo systemctl start grafana-server
   ```

2. **Configure Azure Monitor as a data source in Grafana:**
   - Open Grafana in web browser, in my case the link is: `http://130.107.138.0:3000`
   - Log in with default credentials (`admin/admin`)
   - Navigate to **Configuration > Data Sources**
   - Click **Add data source** and select **Azure Monitor**
   - Choose **Managed Identity** for authentication
![alt text](image.png)

## Task 4: Create a Grafana Dashboard
1. Select 'Create Dashboard'
2. Select "new visualization" and select Azure Monitor as the data source
3. Choose metrics to use in the dashboard. **In my dashboard I selected Network Out Total Network In Total, CPU Percentage, Available Memory Percentage**
4. Adjust the time range and visualization settings
5. Save the dashboard

![alt text](4a.png)
![alt text](4b.png)
![alt text](4d.png)

### Set the Correct Time Range and Time Granularity in the Dashboard
- Set the **time range** at the top to 'Last 3 hours'.
- Adjust the **time grain** in each query, each of my metrics were set to '1 minute'. 
![Queries](image-1.png)
![Graph](image-3.png)
---

## Metrics and Graph Analysis
CPU usage is the proportion of the VM’s processing power that is actively in use. Memory usage measures how much RAM is currently occupied by running processes. Network In Total measures how much data is received by the VM or amount of inbound traffic, and Network Out Total measures how much data is sent from the VM or outbound traffic.
**The graph shows within a time range of 3 hours, CPU usage hovered between 0.8% and 0.9% with a brief spike near 18:00 or 6pm to 1.67%. Available memory had slow slight gradual increase from 33%–35%. Inbound network traffic remained low hovering at around 50 KB, peaking at 174 KB at 18:00. Outbound traffic remained at around 130 KB, also peaking at 245 KB at 18:00.**
These metrics suggest a light workload with occasional bursts of slight activity on the Ubuntu server. 
Overall this lab was successful and I encountered no issues in the setup and creation of the final dashboard. 
