# Helm Chart Configuration Setup Guide

This README provides a complete guide to setting up a local Kubernetes environment using **MicroK8s**, installing **Helm** for Kubernetes package management, and deploying a Helm chart.

Follow the steps below to install and configure the necessary tools and deploy a Kubernetes application with Helm.

---

## Prerequisites

- **Windows Subsystem for Linux (WSL)** installed on Windows.
- **Ubuntu** environment running under WSL.
- **Kubernetes** installed in your system.

---

## Step-by-Step Instructions

### 1. Install Multipass

Multipass allows you to create and manage virtual machines on your local machine, useful for managing Kubernetes clusters in virtualized environments.

**To install Multipass:**

1. Open your **Ubuntu terminal** under WSL and install **Multipass** using **Snap**:

    ```bash
    sudo snap install multipass
    ```

2. Once the installation is complete, check if any virtual machines (VMs) are running by executing the following command:

    ```bash
    multipass list
    ```

    If no instances are listed, it means no virtual machines are running, and you can proceed with the installation of **MicroK8s**.

---

### 2. Install MicroK8s

**MicroK8s** is a lightweight Kubernetes distribution suitable for local environments.

**To install MicroK8s:**

1. Run the following command to install **MicroK8s**:

    ```bash
    sudo snap install microk8s --classic
    ```

2. Once the installation is complete, verify that **MicroK8s** is up and running by checking its status:

    ```bash
    microk8s status --wait-ready
    ```

    This command will wait until **MicroK8s** is fully initialized and ready for use.

---

### 3. Enable Kubernetes Dashboard in MicroK8s

Now that **MicroK8s** is installed, you can enable the Kubernetes Dashboard for easier cluster management.

**To enable the Kubernetes Dashboard:**

1. Run the following command to enable the dashboard:

    ```bash
    microk8s enable dashboard
    ```

2. Once the dashboard is enabled, you can access it by running:

    ```bash
    sudo microk8s dashboard-proxy
    ```

    This will create a proxy and provide you with a URL (e.g., `http://127.0.0.1:8001`) to access the dashboard in your browser. Along with the URL, a **token** will be generated that you will need to log into the dashboard later.

---

### 4. Start Minikube in Parallel

To run another local Kubernetes cluster, you can install **Minikube**. This allows you to have multiple Kubernetes clusters running in parallel.

**To start Minikube:**

1. Open another terminal window or tab, and start **Minikube** by running:

    ```bash
    minikube start
    ```

    This will set up a separate local Kubernetes environment alongside **MicroK8s**. You can switch between **MicroK8s** and **Minikube** as needed.

---

### 5. Install Helm

**Helm** is a package manager for Kubernetes, making it easier to deploy and manage applications.

**To install Helm on Windows:**

1. Go to the official [Helm Releases page](https://github.com/helm/helm/releases).

2. Download the latest release for Windows. Choose the appropriate zip file for your architecture (e.g., `helm-v3.x.x-windows-amd64.zip`).

3. Once the zip file is downloaded, unzip it and move the `helm.exe` binary to a directory of your choice (e.g., `C:\helm\`).

4. To verify the installation, run the following command in your terminal:

    ```bash
    helm version
    ```

    This will output the version information for Helm, confirming the installation was successful.

---

### 6. Create a Helm Chart for Testing

Now that **Helm** is installed, let's create a simple Helm chart to deploy an application.

**To create a Helm chart:**

1. Use the following command to create a new Helm chart called `helloworld`:

    ```bash
    helm create helloworld
    ```

2. Navigate to the `helloworld` directory:

    ```bash
    cd helloworld
    ```

3. Open the `values.yaml` file in a text editor to modify the configuration:

    ```bash
    notepad values.yaml
    ```

4. In the `values.yaml` file, change the `service.type` from `ClusterIP` to `NodePort` to expose the service on a specific port outside the Kubernetes cluster:

    ```yaml
    service:
      type: NodePort
    ```

    Save the file after making this change.

---

### 7. Install the Helm Chart

After modifying the `values.yaml` file, you can install the Helm chart and deploy it on your Kubernetes cluster.

**To install the Helm chart:**

1. Run the following command to install the chart with the name `myhelloworld`:

    ```bash
    helm install myhelloworld helloworld
    ```

2. To verify that the chart was installed successfully, list all installed charts:

    ```bash
    helm list -a
    ```

3. You can also check the deployed services by running:

    ```bash
    kubectl get services
    ```

---

### 8. Port Forwarding to Access the Application

To access the **Nginx** application running inside the Kubernetes cluster, you need to forward the port from the Kubernetes pod to your local machine.

**To set up port forwarding:**

1. First, find the pod associated with your deployed service:

    ```bash
    kubectl get pods
    ```

2. Then, forward the port to your local machine by running the following command. Replace `<your-pod-name>` with the actual pod name from the previous command:

    ```bash
    kubectl port-forward pod/<your-pod-name> port:Nodeport
    ```

    This will forward port `port` on your local machine to port Nodeport on the Kubernetes pod.

3. You can now access the application by navigating to `http://localhost:port` in your web browser.

---

### 8. Update the Replica Count in the Helm Chart

To update the **ReplicaSet** from `1` to `2` and redeploy the application, follow these steps:

1. Open the `values.yaml` file again:

    ```bash
    notepad values.yaml
    ```

2. Find the `replicaCount` section and change the value from `1` to `2`:

    ```yaml
    replicaCount: 2
    ```

3. Save the `values.yaml` file.

---

### 9. Redeploy with Helm

To apply the updated replica count and redeploy the application, use the following **Helm upgrade** command:

```bash
helm upgrade myhelloworld ./helloworld
```

### 10. Verify the Update

To verify that the update was successful:

## 1. Check the deployed pods:

```bash
kubectl get pods
```

You should now see 2 pods running for your application, reflecting the updated replica count.

## 2. Check the ReplicaSet to ensure that the number of replicas is correctly updated:

```bash
kubectl get replicaset
```

You should see the ReplicaSet with 2 replicas.

### 11. Port Forwarding to Access the Application
To access the Nginx application running inside the Kubernetes cluster, you need to forward the port from the Kubernetes pod to your local machine.

To set up port forwarding:

## 1. First, find the pod associated with your deployed service:

```bash
kubectl get pods
```

## 2. Then, forward the port to your local machine by running the following command. Replace <your-pod-name> with the actual pod name from the previous command:

```bash
kubectl port-forward pod/<your-pod-name> port:Nodeport
```

## You can now access the application by navigating to the link http:127.0.0.1:port in your web browser.


### 12. Access the Kubernetes Dashboard

After running `sudo microk8s dashboard-proxy`, you will see a URL (e.g., `http://127.0.0.1:port`). Open this URL in your browser to access the Kubernetes dashboard.

You do not need to manually retrieve the token, as it will **automatically be generated** when you run the `dashboard-proxy` command.

2. Copy the token and paste it into the dashboard login screen.

3. Once logged in, you can view the deployed Helm application and monitor its status in the deployment workspace.

---

## Conclusion

By following the steps outlined above, you have:

1. Installed **Multipass**, **MicroK8s**, and **Minikube** for local Kubernetes environments.
2. Installed **Helm** for managing Kubernetes applications.
3. Created and deployed a simple Helm chart (`helloworld`).
4. Accessed the application via port forwarding and the Kubernetes dashboard.

Now you can use **Helm** to manage your Kubernetes applications, and explore further Kubernetes features and charts.


