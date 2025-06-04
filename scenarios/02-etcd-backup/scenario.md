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

## Procedure

### ✅ Step 1: Backup etcd

```bash
etcdctl snapshot save snapshot \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key
```

### ✅ Step 2: Verify Snapshot

```bash
etcdctl snapshot status snapshot --write-out=table
```

You should now see a file named `snapshot` in your current directory.

---

### ✅ Step 3: Simulate Data Loss

Delete a critical Kubernetes resource to simulate disaster:

```bash
kubectl delete ds kube-proxy -n kube-system
```

Verify it's deleted:

```bash
kubectl get ds -A
```

---

### ✅ Step 4: Restore etcd from Snapshot

```bash
etcdctl snapshot restore snapshot --data-dir /var/lib/etcd-restore
```

---

### ✅ Step 5: Update etcd Pod Manifest

Edit `/etc/kubernetes/manifests/etcd.yaml` and update the volume path:

```yaml
  - hostPath:
      path: /var/lib/etcd-restore  # CHANGE THIS LINE
      type: DirectoryOrCreate
    name: etcd-data
```

> ⚠️ The Kubernetes API will be unavailable while etcd restarts. This may take 2–3 minutes.

---

### ✅ Step 6: Verify Cluster State

Once the API server is back:

```bash
kubectl get ds -A
```

You should see `kube-proxy` restored.

---

## Expected Outcome

At the end of this task, you will:

* Have successfully taken an etcd backup
* Performed a restore from that backup
* Recovered a deleted Kubernetes resource (kube-proxy)

This is a high-value operation for real-world disaster recovery!

---

## 📚 References

* [https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/](https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/)
* [https://etcd.io/docs/v3.5/op-guide/backup-restore/](https://etcd.io/docs/v3.5/op-guide/backup-restore/)
