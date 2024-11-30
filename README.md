# aws-grafana-project
deploy, monitor, and manage Grafana on an EC2 instance, including logs and alerts.

Setting up Grafana on an AWS EC2 instance involves several steps including installation, configuration, enabling monitoring, logging, analysis, notifications, and alerting. Below is a detailed guide along with code snippets:

1. Set Up the EC2 Instance

	1.	Launch an EC2 Instance:
	•	Choose an Amazon Linux 2 or Ubuntu AMI.
	•	Select an instance type (e.g., t2.micro for testing purposes).
	•	Configure security group:
	•	Allow inbound rules for SSH (port 22) and HTTP (port 3000 for Grafana) & 9090
	2.	SSH into the Instance:
```
ssh -i <your-key.pem> ec2-user@<your-ec2-public-ip>
```
2. Install Grafana

On Amazon Linux 2
// Install Grafana via Yum Repository:

```
sudo yum install -y https://dl.grafana.com/oss/release/grafana-9.5.2-1.x86_64.rpm
```
// Start and Enable Grafana:
```
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
```
On Ubuntu*
// Update and Install Dependencies:
```
sudo apt update
sudo apt install -y software-properties-common
```
// Add Grafana APT Repository and Install:
```
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
echo "deb https://packages.grafana.com/oss/deb stable main" | sudo tee /etc/apt/sources.list.d/grafana.list
sudo apt update
sudo apt install -y grafana
```
// Start and Enable Grafana:
```
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
```
*Configure Monitoring*

a. Install and Configure Prometheus (Optional for Monitoring Data)

// Download Prometheus:
```
wget https://github.com/prometheus/prometheus/releases/download/v2.45.0/prometheus-2.45.0.linux-amd64.tar.gz
tar xvfz prometheus-2.45.0.linux-amd64.tar.gz
cd prometheus-2.45.0.linux-amd64
```

// Run Prometheus:
```
./prometheus --config.file=prometheus.yml
```

// Add Prometheus as a Data Source in Grafana:
	•	Navigate to http://<your-ec2-public-ip>:3000 and log in (admin/admin by default).
	•	Go to Configuration > Data Sources and add Prometheus as a source with http://localhost:9090.

4. Configure Logging

a. Install and Configure Loki for Log Aggregation

	1.	Download Loki:
```
wget https://github.com/grafana/loki/releases/download/v2.9.0/loki-linux-amd64.zip
unzip loki-linux-amd64.zip
chmod +x loki-linux-amd64
```

	2.	Create a Configuration File (loki-config.yaml):
```
auth_enabled: false

server:
  http_listen_port: 3100

ingester:
  lifecycler:
    ring:
      kvstore:
        store: inmemory
      replication_factor: 1

schema_config:
  configs:
    - from: 2020-10-24
      store: boltdb
      object_store: filesystem
      schema: v11
      index:
        prefix: index_
        period: 24h

storage_config:
  boltdb:
    directory: /tmp/loki/index
  filesystem:
    directory: /tmp/loki/chunks

limits_config:
  enforce_metric_name: false
  reject_old_samples: true
  reject_old_samples_max_age: 168h
```

	3.	Start Loki:
```
./loki-linux-amd64 --config.file=loki-config.yaml
```

	4.	Add Loki as a Data Source in Grafana:
	•	Go to Configuration > Data Sources in Grafana.
	•	Add Loki with http://localhost:3100.

5. Set Up Alerts and Notifications

	1.	Create Alerts:
	•	Go to Alerting > Alerts in Grafana.
	•	Set up thresholds, queries, and alert conditions.
	2.	Configure Notification Channels:
	•	Go to Alerting > Notification Channels.
	•	Add a notification channel (e.g., Email, Slack, PagerDuty).
	3.	Set Up SMTP for Email Alerts:
Edit Grafana configuration file (/etc/grafana/grafana.ini):
```
[smtp]
enabled = true
host = smtp.example.com:587
user = your-smtp-username
password = your-smtp-password
from_address = grafana@example.com
```
Restart Grafana:
```
sudo systemctl restart grafana-server
```
6. Monitoring and Analysis

	1.	Dashboards:
	•	Import prebuilt dashboards from Grafana Dashboard Library.
	•	Create custom dashboards with metrics or logs from Prometheus/Loki.
	2.	Query Data:
	•	Use the Query Editor in Grafana to explore logs and metrics.
	•	Build visualizations like graphs, tables, or heatmaps.

7. Automate Setup with a Script

Create a script (setup-grafana.sh) for installation and configuration:
```
#!/bin/bash

# Update and install Grafana
sudo apt update
sudo apt install -y software-properties-common
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
echo "deb https://packages.grafana.com/oss/deb stable main" | sudo tee /etc/apt/sources.list.d/grafana.list
sudo apt update
sudo apt install -y grafana

# Start and enable Grafana
sudo systemctl start grafana-server
sudo systemctl enable grafana-server

# Install Prometheus (optional)
wget https://github.com/prometheus/prometheus/releases/download/v2.45.0/prometheus-2.45.0.linux-amd64.tar.gz
tar xvfz prometheus-2.45.0.linux-amd64.tar.gz
cd prometheus-2.45.0.linux-amd64

# Install Loki
wget https://github.com/grafana/loki/releases/download/v2.9.0/loki-linux-amd64.zip
unzip loki-linux-amd64.zip
chmod +x loki-linux-amd64

# Print access information
echo "Grafana is available at http://<EC2-PUBLIC-IP>:3000"
```
Run the script:
```
bash setup-grafana.sh
```
8. Secure Grafana

	1.	Update the Admin Password:
	•	Login to Grafana and change the default password.
	2.	Enable HTTPS:
	•	Configure an NGINX reverse proxy or enable HTTPS directly in Grafana with a valid SSL certificate.

With these steps, you can fully deploy, monitor, and manage Grafana on an EC2 instance, including logs and alerts.
