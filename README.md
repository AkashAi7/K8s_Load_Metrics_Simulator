# K8s_Load_Metrics_Simulator
This repo includes the yaml files for simulating the pressure over the nodes this includes the metrics like CPU , memory etc
# K8s_Load_Metrics_Simulator

## Overview
`K8s_Load_Metrics_Simulator` is a collection of Kubernetes manifest files designed to simulate high resource usage (CPU, memory, disk latency) and trigger OOM (Out of Memory) kills for pods. This allows for testing node resilience, performance under stress, and validating autoscaling policies.

By default, the manifests are configured to simulate pressure on Azure Kubernetes Service (AKS) nodes with SKU `Standard_D2as_v4` (2 vCPUs, 8 GiB RAM).  
You can adjust the manifests to match your node specifications by modifying the parameters according to your Azure VM SKU.

---

## Features
- **CPU Pressure**: Simulates high CPU utilization.  
- **Memory Pressure**: Forces memory exhaustion on nodes.  
- **Disk Latency**: Creates artificial I/O latency.  
- **Pod OOM Kill**: Triggers pod termination by exceeding memory limits.  

---

## Getting Started

### Prerequisites
- Kubernetes cluster (AKS preferred)  
- `kubectl` configured for your cluster  
- Node names or labels for targeting specific nodes  

### Installation
1. Clone the repository:  
   ```bash
   git clone https://github.com/your-repo/K8s_Load_Metrics_Simulator.git
   cd K8s_Load_Metrics_Simulator



2. Modify the nodeSelector in the manifest files to point to your target node:

nodeSelector:
  kubernetes.io/hostname: <your-node-name>

3. Apply the manifests to your cluster:



kubectl apply -f cpu-pressure.yaml
kubectl apply -f memory-pressure.yaml
kubectl apply -f disk-pressure.yaml
kubectl apply -f pod-oom-kill.yaml
Customization
If you are targeting nodes with different SKUs, adjust the parameters in the manifest files to reflect the node capacity.
Example:

Standard_D8s_v4 (8 vCPUs, 32 GiB RAM)
Standard_E16_v3 (16 vCPUs, 128 GiB RAM)
Azure SKU Load Approximation Table
Use this table to estimate the resource load required for different Azure VM SKUs. Adjust the manifest requests and limits based on these values.

Azure SKU	vCPUs	Memory (GiB)	Recommended CPU Pressure (millicores)	Recommended Memory Pressure (Mi)
Standard_D2as_v4	2	8	1800	6500
Standard_D4as_v4	4	16	3600	13000
Standard_D8as_v4	8	32	7200	26000
Standard_E16_v3	16	128	15000	105000
Standard_D32_v3	32	128	30000	110000

### Troubleshooting
Pod Stuck in Pending: Check if there are enough resources available on the node. Use:


kubectl describe pod <pod-name>
CrashLoopBackOff: If the pod keeps restarting, reduce the resource limits in the manifest or allocate more resources to the node.
Contribution
Feel free to submit pull requests to improve the manifest files or add new types of stress simulations.

### License
MIT License



---

### Pressure Load Calculator (Concept)
You can create a Python or Bash script to automate the calculation of pressure parameters based on node SKU. Here's a simple example in Python:

```python
def calculate_pressure(vcpus, memory_gb):
    cpu_pressure = (vcpus * 900)  # 900 millicores per vCPU
    memory_pressure = (memory_gb * 800)  # 800 Mi per GiB of memory
    return cpu_pressure, memory_pressure

sku_data = {
    "Standard_D2as_v4": (2, 8),
    "Standard_D8as_v4": (8, 32),
    "Standard_E16_v3": (16, 128),
}

for sku, specs in sku_data.items():
    cpu, mem = calculate_pressure(*specs)
    print(f"{sku}: CPU {cpu}m | Memory {mem}Mi")