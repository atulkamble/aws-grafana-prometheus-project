To run `./loki-linux-amd64` with the `--config.file=loki-config.yaml` flag in the background, you can use similar approaches as with Prometheus. Here’s how you can do it:

---

### **1. Basic Background Execution Using `&`**
Run the Loki process in the background using `&`:
```bash
./loki-linux-amd64 --config.file=loki-config.yaml &
```

---

### **2. Detaching from the Terminal Using `nohup`**
Use `nohup` to ensure the process continues running even if the terminal is closed:
```bash
nohup ./loki-linux-amd64 --config.file=loki-config.yaml > loki.log 2>&1 &
```
- `nohup`: Prevents the process from being terminated when the terminal session ends.
- `> loki.log`: Redirects standard output to `loki.log`.
- `2>&1`: Redirects standard error to the same file as standard output.

---

### **3. Using `screen` or `tmux`**
Run Loki in a session manager for better control.

#### **Using `screen`:**
- Start a new session:
  ```bash
  screen -S loki
  ```
- Run the command:
  ```bash
  ./loki-linux-amd64 --config.file=loki-config.yaml
  ```
- Detach the session by pressing `Ctrl+A`, then `D`.
- Reattach the session later with:
  ```bash
  screen -r loki
  ```

#### **Using `tmux`:**
- Start a new tmux session:
  ```bash
  tmux new -s loki
  ```
- Run the command:
  ```bash
  ./loki-linux-amd64 --config.file=loki-config.yaml
  ```
- Detach the session by pressing `Ctrl+B`, then `D`.
- Reattach the session later with:
  ```bash
  tmux attach -t loki
  ```

---

### **4. Using `systemd` (Recommended for Production)**
Set up Loki as a service for consistent and reliable management.

#### **Create a systemd Service File:**
1. Create a new service file:
   ```bash
   sudo nano /etc/systemd/system/loki.service
   ```

2. Add the following content:
   ```ini
   [Unit]
   Description=Loki Service
   After=network.target

   [Service]
   ExecStart=/path/to/loki-linux-amd64 --config.file=/path/to/loki-config.yaml
   WorkingDirectory=/path/to
   Restart=always
   User=your_user
   Group=your_group

   [Install]
   WantedBy=multi-user.target
   ```

3. Replace `/path/to` with the actual path to your Loki binary and configuration file.

---

#### **Enable and Start the Service:**
- Reload systemd:
  ```bash
  sudo systemctl daemon-reload
  ```
- Start Loki:
  ```bash
  sudo systemctl start loki
  ```
- Enable it to start at boot:
  ```bash
  sudo systemctl enable loki
  ```

---

#### **Check Service Status:**
- View service status:
  ```bash
  sudo systemctl status loki
  ```

---

### **Best Practice**
For production environments, using **systemd** is highly recommended for reliability, automatic restarts, and better integration with system tools. For quick testing or development, `nohup` or `tmux`/`screen` is more straightforward.
