// Data source connected, but no labels received. Verify that Loki and Promtail is configured properly.

The error message **"Data source connected, but no labels received"** typically indicates that **Grafana** can connect to your **Loki data source**, but **Promtail** (or another log ingestion tool) is either not sending logs to Loki or not sending them with the correct labels. Here's a step-by-step troubleshooting guide:

---

### **1. Check Promtail Configuration**
- Ensure Promtail is properly configured to send logs to Loki:
  - Open your `promtail-config.yaml` file and verify the `clients` section:
    ```yaml
    clients:
      - url: http://<LOKI_IP>:3100/loki/api/v1/push
    ```
    Replace `<LOKI_IP>` with the IP address or hostname of your Loki server.

  - Check the `scrape_configs` section to ensure it's correctly configured for the log files or sources:
    ```yaml
    scrape_configs:
      - job_name: system_logs
        static_configs:
          - targets:
              - localhost
            labels:
              job: system_logs
              host: my-server
              __path__: /var/log/*.log
    ```

    - Ensure the `__path__` points to valid log files.
    - Add meaningful labels like `job` and `host` for querying in Grafana.

---

### **2. Verify Loki Logs**
- Check Loki logs to confirm it is receiving data from Promtail:
  ```bash
  tail -f /path/to/loki/logs.log
  ```
  Look for any errors related to ingestion or missing connections.

---

### **3. Verify Promtail Logs**
- Check Promtail logs for errors:
  ```bash
  tail -f /path/to/promtail/logs.log
  ```
  Common issues include:
  - Failure to connect to Loki.
  - File path not found or no new logs being scraped.

---

### **4. Test Loki Ingestion API**
- Test if Loki is accepting log data by sending a sample log via `curl`:
  ```bash
  curl -X POST "http://<LOKI_IP>:3100/loki/api/v1/push" \
  -H "Content-Type: application/json" \
  -d '{
        "streams": [
          {
            "stream": {
              "job": "test"
            },
            "values": [
              ["1698657000000000000", "Test log entry"]
            ]
          }
        ]
      }'
  ```
  If Loki logs this entry, the issue is likely with Promtail.

---

### **5. Check Grafana Query**
- In Grafana, verify your query to Loki:
  - Open **Explore**.
  - Choose **Loki** as the data source.
  - Run a query to check logs:
    ```logql
    {job="system_logs"}
    ```
  - If no logs appear, refine your query or labels.

---

### **6. Common Issues to Check**
- **Promtail Service Not Running**:
  Ensure Promtail is running:
  ```bash
  systemctl status promtail
  ```
  If not, start it:
  ```bash
  systemctl start promtail
  ```

- **Firewall or Network Block**:
  Ensure there is no firewall or network issue blocking communication between Promtail and Loki.

- **Mismatched Labels**:
  Ensure the labels in Promtail’s configuration match those queried in Grafana.

---

### **7. Enable Debug Logging**
For more detailed logs, enable debug mode in both Loki and Promtail:

- **Loki**: Add `-log.level=debug` to your Loki command or configuration.
- **Promtail**: Add `-log.level=debug` to your Promtail command or configuration.

---

### **8. Restart Services**
After making changes, restart both Loki and Promtail:
```bash
systemctl restart loki
systemctl restart promtail
```

---

### **Expected Result**
Once properly configured, you should see labeled logs in Grafana’s Explore panel when querying the Loki data source. If the problem persists, revisit logs and configurations.
