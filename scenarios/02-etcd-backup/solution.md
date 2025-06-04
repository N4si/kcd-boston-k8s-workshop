# ETCD Backup and Restore - Solution

```bash
# Step 1: Set etcdctl API version
export ETCDCTL_API=3

# Step 2: Take a snapshot backup of etcd
etcdctl snapshot save snapshot \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key

# Step 3: Verify snapshot status
etcdctl snapshot status snapshot --write-out=table

# Step 4: Simulate disaster (delete kube-proxy)
kubectl delete ds kube-proxy -n kube-system

# Step 5: Confirm deletion
kubectl get ds -A

# Step 6: Restore snapshot to a new directory
etcdctl snapshot restore snapshot --data-dir /var/lib/etcd-restore

# Step 7: Update etcd manifest to point to the restored data directory
# Edit this file:
vim /etc/kubernetes/manifests/etcd.yaml

# Look for the volume path under hostPath:
# Change from:
#   path: /var/lib/etcd
# To:
#   path: /var/lib/etcd-restore

# Step 8: Wait for etcd to restart (2–3 minutes)

# Step 9: Verify kube-proxy daemonset is restored
kubectl get ds -A
```

✅ Your etcd backup and restore workflow is complete!
