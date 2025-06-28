# Scenario: Kubernetes Version Upgrade

## Objective

You are managing a Kubernetes cluster running version 1.31.x and need to upgrade it to version 1.32.x. The task is to safely upgrade both the control plane and node components with minimal downtime.

Your job is to:

* Check the current version of the cluster
* Review the available newer version (1.32.2)
* Safely upgrade the kubeadm, kubelet, and kubectl tools
* Upgrade the control plane node
* Drain and upgrade a worker node
* Bring the node back into service
* Verify the upgrade was successful

---

## Background

Kubernetes upgrades are performed incrementally (e.g., from 1.27.x to 1.28.x). You must ensure compatibility and perform pre-flight checks before proceeding.

Upgrading involves:

* Upgrading `kubeadm` (used to bootstrap the cluster)
* Using `kubeadm upgrade` to upgrade the cluster and apply the upgrade plan
* Upgrading `kubelet` and `kubectl`
* Restarting `kubelet` service

---

## Tips

* Always consult the [Kubernetes version skew policy](https://kubernetes.io/docs/setup/release/version-skew-policy/).
* Review the [official upgrade guide](https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/) before performing steps.
* Ensure all nodes are healthy before initiating upgrade.

---

## Prerequisites

* You must have root or sudo privileges on all cluster nodes
* Backups of etcd and critical workloads are completed
* Internet access to download newer version binaries and container images
* All nodes are in a healthy state (`kubectl get nodes` shows all nodes as Ready)
* No critical workloads are running that cannot tolerate brief downtime

---

## Expected Outcome

By the end of this task:

* The cluster will be upgraded from 1.31.x to 1.32.2
* All nodes should be back online and schedulable
* All system pods (kube-system namespace) are running correctly
* You can verify the upgrade with `kubectl version` and `kubectl get nodes`
* You'll understand the order of operations and documentation references for performing a real-world upgrade safely

## Verification Steps

After completing the upgrade, verify success by running:
```bash
kubectl get nodes
kubectl version --short
kubectl get pods -n kube-system
```

All nodes should show the new version, and all system pods should be in Running state.y.

---

Next: Check `solution.md` for step-by-step instructions.
