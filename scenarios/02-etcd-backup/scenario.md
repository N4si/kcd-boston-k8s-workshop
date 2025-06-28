# Scenario: ETCD Backup and Restore

## Objective

You're responsible for the resilience of your Kubernetes cluster. In this scenario, you'll practice backing up and restoring etcd — the key-value store used by Kubernetes to maintain cluster state.

Your goal:

* Perform a snapshot backup of etcd.
* Simulate data loss by deleting a Kubernetes resource.
* Restore etcd from the snapshot.
* Validate that the resource is recovered.

---

## Background

Etcd is the source of truth for Kubernetes. Backups are critical for disaster recovery and cluster state preservation.

Etcd must be backed up regularly, especially before upgrades or large configuration changes.

> The `etcdctl` tool is used to interact with etcd directly. It's typically available on control plane nodes.

---

## Tips

* Always run etcdctl commands as root or with sudo.
* Use the correct etcd endpoint and certs when running etcdctl.
* The snapshot is a binary file — store it securely.

> 🔥 TIP: etcd requires authentication via TLS. Certificates are typically found in `/etc/kubernetes/pki/etcd`.

---

## Prerequisites

* Access to the control plane node.
* `etcdctl` installed and certificates available.

Common certificate paths:

* `/etc/kubernetes/pki/etcd/ca.crt`
* `/etc/kubernetes/pki/etcd/server.crt`
* `/etc/kubernetes/pki/etcd/server.key`

Set API version:

```bash
export ETCDCTL_API=3
```

---

## Your Tasks

1. **Create an etcd backup** using the `etcdctl` tool with proper certificates
2. **Verify the backup** was created successfully and contains valid data
3. **Simulate a disaster** by deleting a critical Kubernetes resource
4. **Restore from the backup** to recover the lost resource
5. **Verify the restoration** was successful and the cluster is fully functional

## Challenge

The real challenge is understanding the etcd certificate paths, using the correct API version, and safely updating the etcd pod manifest without breaking the cluster permanently.

---

## Expected Outcome

At the end of this task, you will:

* Have successfully taken an etcd backup with proper authentication
* Verified the backup integrity using `etcdctl snapshot status`
* Performed a restore from that backup to a new data directory
* Recovered a deleted Kubernetes resource (kube-proxy DaemonSet)
* Understand the critical importance of etcd backups for disaster recovery

## Verification Steps

After completing the restore, verify success by:
```bash
# Check that kube-proxy DaemonSet is restored
kubectl get ds -A | grep kube-proxy

# Verify all nodes are ready
kubectl get nodes

# Check that all system pods are running
kubectl get pods -n kube-system
```

This is a high-value operation for real-world disaster recovery!

---

## 📚 References

* [https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/](https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/)
* [https://etcd.io/docs/v3.5/op-guide/backup-restore/](https://etcd.io/docs/v3.5/op-guide/backup-restore/)
