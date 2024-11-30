To run the `./prometheus` process with a configuration file (`prometheus.yml`) in the background, you can follow these steps:

---

### **Steps**

1. **Basic Background Execution Using `&`:**
   Use the `&` operator to run the command in the background:
   ```bash
   ./prometheus --config.file=prometheus.yml &
   ```

2. **Detaching from the Terminal (Using `nohup`):**
   To ensure the process continues running even if the terminal session is closed:
   ```bash
   nohup ./prometheus --config.file=prometheus.yml > prometheus.log 2>&1 &
   ```
   - `nohup`: Prevents the process from stopping when the terminal is closed.
   - `> prometheus.log`: Redirects standard output to a file.
   - `2>&1`: Redirects standard error to the same file as standard output.

3. **Using `screen` or `tmux`:**
   If you need to monitor the process later, run it inside a session manager like `screen` or `tmux`.
   - **Using `screen`:**
     ```bash
     screen -S prometheus
     ./prometheus --config.file=prometheus.yml
     ```
     - Detach the session by pressing `Ctrl+A` and then `D`.
     - Reattach with:
       ```bash
       screen -r prometheus
       ```
   - **Using `tmux`:**
     ```bash
     tmux new -s prometheus
     ./prometheus --config.file=prometheus.yml
     ```
     - Detach the session by pressing `Ctrl+B` and then `D`.
     - Reattach with:
       ```bash
       tmux attach -t prometheus
       ```

4. **Using a Service Manager (Recommended for Production):**
   Set up `prometheus` as a service using **systemd** for better management.

   - Create a systemd service file:
     ```bash
     sudo nano /etc/systemd/system/prometheus.service
     ```
   - Add the following content:
     ```ini
     [Unit]
     Description=Prometheus Server
     After=network.target

     [Service]
     ExecStart=/path/to/prometheus --config.file=/path/to/prometheus.yml
     WorkingDirectory=/path/to/prometheus
     Restart=always
     User=ec2-user
     Group=ec2-user

     [Install]
     WantedBy=multi-user.target
     ```

   - Reload systemd and start the service:
     ```bash
     sudo systemctl daemon-reload
     sudo systemctl start prometheus
     sudo systemctl enable prometheus
     ```

   - Check status:
     ```bash
     sudo systemctl status prometheus
     ```

---

### **Best Practice**
Using a service manager like `systemd` is the most reliable and production-grade approach. It ensures the process restarts on failure and integrates well with system monitoring tools.
