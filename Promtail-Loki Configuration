To configure Loki and Promtail on an EC2 instance, follow these steps:

---

### **1. Prerequisites**
- **AWS EC2 Instance**:
  - Use an instance with at least 2 GB of RAM (e.g., t2.medium).
  - Ensure the instance is running a Linux OS like Ubuntu or Amazon Linux.
- **Firewall Rules**:
  - Allow inbound traffic on required ports (Loki default is `3100`, Promtail HTTP default is `9080`).

---

### **2. Install Loki**
1. **Download Loki Binary**:
   ```bash
   wget https://github.com/grafana/loki/releases/download/v2.8.0/loki-linux-amd64.zip
   unzip loki-linux-amd64.zip
   chmod +x loki-linux-amd64
   sudo mv loki-linux-amd64 /usr/local/bin/loki
   ```

2. **Create a Configuration File**:
   Save the following as `loki-config.yaml` in `/etc/loki/`:
   ```yaml
   auth_enabled: false

   server:
     http_listen_port: 3100

   ingester:
     lifecycler:
       ring:
         kvstore:
           store: inmemory
         replication_factor: 1
     chunk_idle_period: 5m
     chunk_retain_period: 30s
     max_transfer_retries: 0

   schema_config:
     configs:
       - from: 2020-10-24
         store: boltdb
         object_store: filesystem
         schema: v11
         index:
           prefix: index_
           period: 168h

   storage_config:
     boltdb:
       directory: /data/loki/index
     filesystem:
       directory: /data/loki/chunks

   limits_config:
     enforce_metric_name: false
     reject_old_samples: true
     reject_old_samples_max_age: 168h

   chunk_store_config:
     max_look_back_period: 0s

   table_manager:
     retention_deletes_enabled: false
     retention_period: 0s
   ```

3. **Start Loki**:
   ```bash
   loki --config.file=/etc/loki/loki-config.yaml
   ```

---

### **3. Install Promtail**
1. **Download Promtail Binary**:
   ```bash
   wget https://github.com/grafana/loki/releases/download/v2.8.0/promtail-linux-amd64.zip
   unzip promtail-linux-amd64.zip
   chmod +x promtail-linux-amd64
   sudo mv promtail-linux-amd64 /usr/local/bin/promtail
   ```

2. **Create a Configuration File**:
   Save the following as `promtail-config.yaml` in `/etc/promtail/`:
   ```yaml
   server:
     http_listen_port: 9080
     grpc_listen_port: 0

   clients:
     - url: http://<EC2_PRIVATE_IP>:3100/loki/api/v1/push

   positions:
     filename: /data/promtail/positions.yaml

   scrape_configs:
     - job_name: system
       static_configs:
         - targets:
             - localhost
           labels:
             job: varlogs
             host: ec2-instance
             __path__: /var/log/*.log
   ```

3. **Start Promtail**:
   ```bash
   promtail --config.file=/etc/promtail/promtail-config.yaml
   ```

---

### **4. Access Logs in Grafana**
1. **Install Grafana**:
   Follow the [Grafana installation guide](https://grafana.com/docs/grafana/latest/installation/).

2. **Add Loki as a Data Source**:
   - Navigate to **Configuration > Data Sources** in Grafana.
   - Add Loki with the URL: `http://<EC2_PRIVATE_IP>:3100`.

3. **Explore Logs**:
   - Use queries like `{job="varlogs"}` in the **Explore** section of Grafana.

---

### **5. Verify Setup**
- **Promtail Logs**: Check if logs are being pushed:
  ```bash
  tail -f /var/log/promtail.log
  ```
- **Loki Logs**: Confirm Loki is ingesting logs:
  ```bash
  curl http://<EC2_PRIVATE_IP>:3100/metrics
  ```

---

### **6. Troubleshooting**
- **No Logs in Grafana**:
  - Check the Promtail logs for connectivity issues with Loki.
  - Verify `loki-config.yaml` and `promtail-config.yaml` paths and content.
- **Permission Errors**:
  - Ensure Promtail has access to `/var/log/` and `/data/promtail/positions.yaml`.

Let me know if you face any challenges during the setup!
