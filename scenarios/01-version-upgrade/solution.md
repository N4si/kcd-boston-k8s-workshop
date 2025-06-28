# Solution: Kubernetes Version Upgrade

Follow these steps to safely upgrade your Kubernetes cluster using `kubeadm`. This solution follows the official Kubernetes upgrade procedures.

> **Important**: Kubernetes versions are expressed as `x.y.z`, where `x` is the major version, `y` the minor version, and `z` the patch version. You can only upgrade one minor version at a time (e.g., 1.31.x → 1.32.x). See the [version skew policy](https://kubernetes.io/releases/version-skew-policy).

> **Note**: Replace version numbers with those applicable to your upgrade path. For example, `1.32.2` is used below. Always check the [Kubernetes releases page](https://kubernetes.io/releases/) for the latest stable versions.

---

## ✅ Step 1: Check Current Cluster Version

First, verify your current cluster version and ensure all nodes are healthy:

```bash
# Check the version of all nodes
kubectl get nodes

# Check client and server versions
kubectl version --short

# Verify cluster health before upgrade
kubectl get pods -n kube-system
```

**Expected output**: All nodes should show the same version (e.g., v1.31.x) and be in `Ready` state.

---

## ✅ Step 2: Show Available kubeadm Versions

Check what kubeadm versions are available for upgrade:

```bash
# Show available kubeadm versions
apt-cache show kubeadm | grep Version

# Alternative: Show only the latest versions
apt-cache madison kubeadm | head -5
```

**What to look for**: Find the target version (1.32.2-1.1 in this example) in the available versions list.

---

## ✅ Step 3: Install Correct kubeadm Version on Control Plane

```bash
sudo apt update
sudo apt install -y kubeadm=1.32.2-1.1
```

---

## ✅ Step 4: Review Upgrade Plan

Before applying the upgrade, review what will change:

```bash
sudo kubeadm upgrade plan
```

**What this shows**:
- Available upgrade versions
- Component versions that will be upgraded
- Manual upgrade steps required for kubelet and kubectl
- Important notes about the upgrade process

**Important**: Read the output carefully and ensure you understand what will change before proceeding.

---

## ✅ Step 5: Apply the Upgrade

Apply the upgrade to the control plane:

```bash
sudo kubeadm upgrade apply v1.32.2
```

**What happens during this step**:
- Downloads new container images for control plane components
- Updates static pod manifests for API server, controller manager, scheduler
- Updates kubeadm and kubelet configuration
- Performs pre-flight checks

> **Note**: This step may take 5-10 minutes to complete. The API server will be briefly unavailable during the upgrade.

---

## ✅ Step 6: Upgrade kubelet and kubectl on Control Plane

Upgrade the kubelet and kubectl binaries on the control plane node:

```bash
# Upgrade kubelet and kubectl to match the cluster version
sudo apt install -y kubelet=1.32.2-1.1 kubectl=1.32.2-1.1

# Reload systemd daemon to pick up new kubelet configuration
sudo systemctl daemon-reexec

# Restart kubelet service
sudo systemctl restart kubelet

# Verify kubelet is running
sudo systemctl status kubelet
```

**Why these steps**: The `kubeadm upgrade apply` command only upgrades control plane components. The kubelet and kubectl must be upgraded separately.

---

## ✅ Step 7: Upgrade Worker Nodes (Repeat for Each Node)

### 7.1 Drain the Node

Safely evict all pods from the worker node before upgrading:

```bash
# Replace <worker-node-name> with the actual node name
kubectl drain <worker-node-name> --ignore-daemonsets --delete-emptydir-data

# Verify the node is cordoned and pods are evicted
kubectl get nodes
kubectl get pods -o wide | grep <worker-node-name>
```

**What this does**:
- Marks the node as unschedulable (cordoned)
- Evicts all pods except DaemonSets
- Deletes pods with emptyDir volumes (use with caution in production)

### 7.2 Install kubeadm

```bash
sudo apt update
sudo apt install -y kubeadm=1.32.2-1.1
```

### 7.3 Upgrade the Node

Upgrade the worker node configuration:

```bash
# Upgrade the node's local kubelet configuration
sudo kubeadm upgrade node

# Verify the upgrade was successful
sudo kubeadm version
```

**What this does**: Updates the local kubelet configuration to match the new cluster version.

### 7.4 Upgrade kubelet and kubectl

```bash
sudo apt install -y kubelet=1.32.2-1.1 kubectl=1.32.2-1.1
sudo systemctl daemon-reexec
sudo systemctl restart kubelet
```

### 7.5 Uncordon the Node

Make the node schedulable again:

```bash
# Remove the cordon from the node
kubectl uncordon <worker-node-name>

# Verify the node is ready and schedulable
kubectl get nodes
```

**Result**: The node should show as `Ready` and `SchedulingEnabled`.

---

## ✅ Step 8: Verify the Upgrade

Confirm the upgrade was successful across the entire cluster:

```bash
# Check all nodes are running the new version
kubectl get nodes

# Verify client and server versions
kubectl version --short

# Check that all system pods are running
kubectl get pods -n kube-system

# Verify cluster functionality
kubectl get namespaces
kubectl get services
```

**Success criteria**:
- All nodes show the new version (v1.32.2)
- All nodes are in `Ready` state
- All system pods in kube-system namespace are `Running`
- kubectl commands work normally

---

## 📚 Additional Tips and Best Practices

* **Always backup etcd** before upgrading production clusters
* **Test upgrades** in a staging environment first
* **Read release notes** for breaking changes: [Kubernetes Release Notes](https://kubernetes.io/releases/notes/)
* **Monitor cluster health** during and after the upgrade
* **Have a rollback plan** ready in case of issues

## 📖 Official Documentation References

* [Upgrading kubeadm clusters](https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/) - Complete upgrade guide
* [Version Skew Policy](https://kubernetes.io/releases/version-skew-policy/) - Supported version combinations
* [Kubernetes Releases](https://kubernetes.io/releases/) - Latest version information
* [Cluster Administration](https://kubernetes.io/docs/tasks/administer-cluster/) - General cluster management tasks

## ⚠️ Troubleshooting

If the upgrade fails:
1. Check kubelet logs: `sudo journalctl -u kubelet -f`
2. Check control plane pod logs: `kubectl logs -n kube-system <pod-name>`
3. Verify etcd health: `kubectl get cs` (deprecated but still useful)
4. Consult the [troubleshooting guide](https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/troubleshooting-kubeadm/)

---

## 🧪 Try It Yourself

Practice this process in a simulated environment using the [KillerKoda demo](killerkoda-link.md).
