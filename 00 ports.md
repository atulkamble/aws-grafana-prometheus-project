Here are the details for the common ports you mentioned:

### **1. Port 9090:**
- **Used by**: Prometheus Web UI
- **Function**: Port 9090 is typically used for Prometheus' web interface, where you can access the Prometheus dashboard, query your metrics, and view system statistics.
- **Example Usage**: You would use this port to access the Prometheus server UI for managing and monitoring your metrics collection.

### **2. Port 3100:**
- **Used by**: Loki
- **Function**: Port 3100 is the default HTTP port for Loki, the log aggregation system. It is used for accepting log data pushed by Promtail and other log sources. You also access the Loki metrics endpoint via this port.
- **Example Usage**: Grafana connects to Loki on port 3100 to pull log data for visualization.

### **3. Port 9080:**
- **Used by**: Promtail HTTP Server
- **Function**: Port 9080 is used by Promtail to expose its HTTP endpoint, typically for health checks and metrics collection. Promtail runs on this port when it's configured to listen for monitoring and scraping requests.
- **Example Usage**: When running Promtail, this port is used for web access to Promtail’s metrics, including health status and logs processing details.

---

### **Summary of Ports in Context**:
- **9090**: Prometheus web interface.
- **3100**: Loki for logs ingestion and querying.
- **9080**: Promtail’s HTTP port for monitoring and health status.

These ports are commonly used in monitoring and logging setups involving Prometheus, Loki, and Promtail. If you need further details, please refer to official documentation or your system configurations.
