Setting up and using **Prometheus** on **Amazon Linux** involves several steps, including installation, configuration, and monitoring. Below is a step-by-step guide.

---

## **1. Install Prometheus on Amazon Linux**
First, update your system and install necessary dependencies.

```
cd Downloads
chmod 400 prometheus.pem
ssh -i "prometheus.pem" ec2-user@ec2-18-234-161-14.compute-1.amazonaws.com
```

```bash
sudo yum update -y
sudo yum install -y wget tar
```

Download and extract the latest Prometheus binary:

// Ref https://prometheus.io/download/

```bash
cd /usr/local
sudo wget https://github.com/prometheus/prometheus/releases/latest/download/prometheus-3.1.0.linux-amd64.tar.gz
sudo tar -xvzf prometheus-3.1.0.linux-amd64.tar.gz
sudo mv prometheus-3.1.0.linux-amd64 prometheus
```

Check if Prometheus is working:

```bash
/usr/local/prometheus/prometheus --version
```

---

## **2. Configure Prometheus**
Create a **Prometheus configuration file**:

```bash
sudo nano /usr/local/prometheus/prometheus.yml
```

Add the following basic configuration:

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: "prometheus"
    static_configs:
      - targets: ["localhost:9090"]
```

Save and exit (`Ctrl+X`, then `Y` and `Enter`).

---

## **3. Create a Systemd Service for Prometheus**
To run Prometheus as a system service, create a service file:

```bash
sudo nano /etc/systemd/system/prometheus.service
```

Paste the following:

```ini
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=root
ExecStart=/usr/local/prometheus/prometheus --config.file=/usr/local/prometheus/prometheus.yml --storage.tsdb.path=/usr/local/prometheus/data
Restart=always

[Install]
WantedBy=multi-user.target
```

Save and exit.

Reload systemd and start Prometheus:

```bash
sudo systemctl daemon-reload
sudo systemctl start prometheus
sudo systemctl enable prometheus
```

Check the status:

```bash
sudo systemctl status prometheus
```

---

## **4. Access Prometheus Dashboard**
Prometheus runs on **port 9090** by default. Open it in your browser:

```
http://<your-ec2-instance-ip>:9090
```

If running on AWS, ensure that **security groups** allow inbound traffic on port **9090**.

---

## **5. Install and Configure Node Exporter (Optional)**
To monitor system metrics, install Node Exporter:

```bash
cd /usr/local
sudo wget https://github.com/prometheus/node_exporter/releases/latest/download/node_exporter-1.8.2.linux-amd64.tar.gz
sudo tar -xvzf node_exporter-1.8.2.linux-amd64.tar.gz
sudo mv node_exporter-1.8.2.linux-amd64 node_exporter
```

Run Node Exporter:

```bash
/usr/local/node_exporter/node_exporter &
```

Now, add Node Exporter to the **Prometheus configuration**:

```bash
sudo nano /usr/local/prometheus/prometheus.yml
```

```yaml
scrape_configs:
  - job_name: "node"
    static_configs:
      - targets: ["localhost:9100"]
```

Restart Prometheus:

```bash
sudo systemctl daemon-reload
sudo systemctl restart prometheus
```

You can now see system metrics at:

```
http://<your-ec2-instance-ip>:9100/metrics
```

---

## **6. Install Grafana for Visualization (Optional)**
To visualize Prometheus metrics, install Grafana:

```bash
sudo yum install -y https://dl.grafana.com/oss/release/grafana-9.0.0-1.x86_64.rpm
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
```

Grafana runs on **port 3000**, so access it at:

```
http://<your-ec2-instance-ip>:3000
```

Default login:  
- **Username:** `admin`  
- **Password:** `admin`

---

### **Next Steps**
- Add **Prometheus as a data source** in Grafana.
- Create dashboards to monitor CPU, memory, and disk usage.
- Use Alertmanager for notifications.

---

Would you like a script to automate this setup? 🚀
