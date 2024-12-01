To run Helm charts in a Minikube cluster, follow these steps:

---

### **1. Start Minikube**
Ensure Minikube is installed and start it:
```bash
minikube start
```
Verify that Minikube is running:
```bash
minikube status
kubectl get nodes
```

---

### **2. Install Helm**
If Helm is not installed, install it:
```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod +x get_helm.sh
./get_helm.sh
```
Verify the installation:
```bash
helm version
```

---

### **3. Add a Helm Chart Repository**
Add the official Helm chart repository:
```bash
helm repo add stable https://charts.helm.sh/stable
helm repo update
```

---

### **4. Deploy a Helm Chart**
Example: Deploying the `nginx` chart.
```bash
helm install my-nginx stable/nginx-ingress
```
- `my-nginx`: Release name for the deployment.
- `stable/nginx-ingress`: The chart to deploy.

---

### **5. Verify the Deployment**
Check the Helm release:
```bash
helm list
```
Check the Kubernetes resources:
```bash
kubectl get all
```

---

### **6. Access the Application**
Expose the service using Minikube:
```bash
minikube service my-nginx-nginx-ingress-controller
```

---

### **7. Uninstall the Chart (Optional)**
To remove the chart:
```bash
helm uninstall my-nginx
```

This setup allows you to deploy and manage Helm charts in your Minikube environment seamlessly.
