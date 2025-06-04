# Scenario: Kubernetes Version Upgrade

## Objective

You are managing a Kubernetes cluster and need to upgrade it to a newer minor version. The task is to safely upgrade both the control plane and node components with minimal downtime.

Your job is to:

* Check the current version of the cluster.
* Review the available newer version.
* Safely upgrade the kubeadm, kubelet, and kubectl tools.
* Upgrade the control plane node.
* Drain and upgrade a worker node.
* Bring the node back into service.

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

* You must have root or sudo privileges on the nodes.
* Backups of etcd and critical workloads are recommended.
* Internet access to pull newer version binaries or images.

---

## Expected Outcome

By the end of this task:

* The cluster will be upgraded to the desired minor version.
* All nodes should be back online and schedulable.
* You’ll understand the order of operations and documentation references for performing a real-world upgrade safely.

---

Next: Check `solution.md` for step-by-step instructions.
