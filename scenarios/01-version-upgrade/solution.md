# Solution: Kubernetes Version Upgrade

Follow these steps to safely upgrade your Kubernetes cluster using `kubeadm`.

> **Tip 1**: Kubernetes versions are expressed as `x.y.z`, where `x` is the major version, `y` the minor version, and `z` the patch version. See the [version skew policy](https://kubernetes.io/releases/version-skew-policy).

> **Note**: Replace version numbers with those applicable to your upgrade path. For example, `1.32.2` is used below.

---

## ✅ Step 1: Check Current Cluster Version

```bash
kubectl get nodes
kubectl version --short
```

---

## ✅ Step 2: Show Available kubeadm Versions

```bash
apt-cache show kubeadm | grep Version
```

---

## ✅ Step 3: Install Correct kubeadm Version on Control Plane

```bash
sudo apt update
sudo apt install -y kubeadm=1.32.2-1.1
```

---

## ✅ Step 4: Review Upgrade Plan

```bash
sudo kubeadm upgrade plan
```

This will show available upgrade versions and highlight what will change.

---

## ✅ Step 5: Apply the Upgrade

```bash
sudo kubeadm upgrade apply v1.32.2
```

> This step may take a few minutes to complete.

---

## ✅ Step 6: Upgrade kubelet and kubectl on Control Plane

```bash
sudo apt install -y kubelet=1.32.2-1.1 kubectl=1.32.2-1.1
sudo systemctl daemon-reexec
sudo systemctl restart kubelet
```

---

## ✅ Step 7: Upgrade Worker Nodes (Repeat for Each Node)

### 7.1 Drain the Node

```bash
kubectl drain <worker-node-name> --ignore-daemonsets --delete-emptydir-data
```

### 7.2 Install kubeadm

```bash
sudo apt update
sudo apt install -y kubeadm=1.32.2-1.1
```

### 7.3 Upgrade the Node

```bash
sudo kubeadm upgrade node
```

### 7.4 Upgrade kubelet and kubectl

```bash
sudo apt install -y kubelet=1.32.2-1.1 kubectl=1.32.2-1.1
sudo systemctl daemon-reexec
sudo systemctl restart kubelet
```

### 7.5 Uncordon the Node

```bash
kubectl uncordon <worker-node-name>
```

---

## ✅ Step 8: Verify the Upgrade

```bash
kubectl get nodes
kubectl version --short
```

All nodes should now reflect the new version.

---

## 📚 Additional Tips

* Run `kubeadm upgrade -h` for help and available flags.
* Refer to the [official kubeadm upgrade guide](https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/) for details.
* Always perform etcd backup before upgrading production clusters.

---

## 🧪 Try It Yourself

Practice this process in a simulated environment using the [KillerKoda demo](killerkoda-link.md).
