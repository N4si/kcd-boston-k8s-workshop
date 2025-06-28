# Solution: ETCD Backup and Restore

This solution demonstrates how to backup and restore etcd, the key-value store that holds all Kubernetes cluster data. Follow the official procedures from the [Kubernetes documentation](https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/).

## ✅ Step 1: Set etcdctl API Version

```bash
# Set the etcdctl API version to v3 (required for snapshots)
export ETCDCTL_API=3

# Verify the API version is set
echo $ETCDCTL_API
```

**Why this matters**: etcdctl v2 and v3 APIs are incompatible. Kubernetes uses etcd v3, so we must use the v3 API.

---

## ✅ Step 2: Take a Snapshot Backup of etcd

```bash
# Create a snapshot backup with proper TLS authentication
etcdctl snapshot save snapshot \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key

# Verify the snapshot file was created
ls -la snapshot
```

**Certificate explanation**:
- `--cacert`: Certificate Authority certificate to verify etcd server
- `--cert`: Client certificate for authentication
- `--key`: Private key for the client certificate

**Note**: These certificate paths are standard for kubeadm-created clusters. Other installations may use different paths.

---

## ✅ Step 3: Verify Snapshot Integrity

```bash
# Check the snapshot status and metadata
etcdctl snapshot status snapshot --write-out=table

# Alternative: JSON output for programmatic use
etcdctl snapshot status snapshot --write-out=json
```

**What to verify**:
- Hash: Ensures snapshot integrity
- Revision: Shows the etcd revision number
- Total Keys: Number of keys in the snapshot
- Total Size: Snapshot file size

---

## ✅ Step 4: Simulate Data Loss

```bash
# Delete a critical Kubernetes resource to simulate disaster
kubectl delete ds kube-proxy -n kube-system

# Wait a moment for the deletion to propagate
sleep 5
```

**Why kube-proxy**: It's a critical DaemonSet that runs on all nodes. Deleting it simulates a significant data loss scenario.

---

## ✅ Step 5: Confirm the Disaster

```bash
# Verify the DaemonSet is gone
kubectl get ds -A

# Check that kube-proxy pods are terminating/gone
kubectl get pods -n kube-system | grep kube-proxy
```

**Expected result**: No kube-proxy DaemonSet should be listed, and kube-proxy pods should be terminating or gone.

---

## ✅ Step 6: Restore from Snapshot

```bash
# Restore the snapshot to a new data directory
etcdctl snapshot restore snapshot --data-dir /var/lib/etcd-restore

# Verify the restore directory was created
ls -la /var/lib/etcd-restore/
```

**Important**: We restore to a new directory (`/var/lib/etcd-restore`) rather than overwriting the existing data directory. This is safer and allows for rollback if needed.

---

## ✅ Step 7: Update etcd Pod Manifest

```bash
# Backup the original etcd manifest
sudo cp /etc/kubernetes/manifests/etcd.yaml /etc/kubernetes/manifests/etcd.yaml.backup

# Edit the etcd manifest to point to the restored data
sudo sed -i 's|/var/lib/etcd|/var/lib/etcd-restore|g' /etc/kubernetes/manifests/etcd.yaml

# Verify the change was made
grep -A 5 -B 5 "etcd-restore" /etc/kubernetes/manifests/etcd.yaml
```

**What this does**: Changes the hostPath volume mount from `/var/lib/etcd` to `/var/lib/etcd-restore`, causing etcd to use the restored data.

**Alternative manual method**:
```bash
# If you prefer to edit manually
sudo vim /etc/kubernetes/manifests/etcd.yaml

# Look for the volumes section and change:
# - hostPath:
#     path: /var/lib/etcd
#     type: DirectoryOrCreate
# To:
# - hostPath:
#     path: /var/lib/etcd-restore
#     type: DirectoryOrCreate
```

---

## ✅ Step 8: Wait for etcd to Restart

```bash
# Monitor etcd pod restart (this may take 2-3 minutes)
watch kubectl get pods -n kube-system | grep etcd

# Alternative: Check kubelet logs for etcd restart
sudo journalctl -u kubelet -f | grep etcd
```

**What happens**: The kubelet detects the manifest change and recreates the etcd pod with the new data directory.

**Warning**: The Kubernetes API will be unavailable during etcd restart. This is normal and expected.

---

## ✅ Step 9: Verify Successful Restore

```bash
# Check that kube-proxy DaemonSet is restored
kubectl get ds -A | grep kube-proxy

# Verify all system components are running
kubectl get pods -n kube-system

# Check cluster functionality
kubectl get nodes
kubectl get namespaces
```

**Success criteria**:
- kube-proxy DaemonSet should be present again
- All system pods should be running
- kubectl commands should work normally

---

## 📚 Best Practices and Additional Information

### Backup Strategy
- **Frequency**: Take etcd backups regularly (daily or before major changes)
- **Storage**: Store backups in a secure, separate location
- **Retention**: Keep multiple backup versions for different recovery points
- **Testing**: Regularly test backup restoration procedures

### Production Considerations
- **Automated backups**: Use tools like [etcd-operator](https://github.com/coreos/etcd-operator) or custom scripts
- **Monitoring**: Monitor etcd health and backup success
- **Documentation**: Document your backup and restore procedures
- **Access control**: Secure access to etcd certificates and backup files

## 📖 Official Documentation References

- [Backing up an etcd cluster](https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/#backing-up-an-etcd-cluster)
- [Restoring an etcd cluster](https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/#restoring-an-etcd-cluster)
- [etcd official documentation](https://etcd.io/docs/v3.5/op-guide/recovery/)
- [Disaster recovery best practices](https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/#disaster-recovery)

## ⚠️ Troubleshooting

If the restore fails:
1. **Check etcd logs**: `kubectl logs -n kube-system etcd-<node-name>`
2. **Verify certificates**: Ensure certificate paths are correct
3. **Check permissions**: Verify etcd can read the restored data directory
4. **Rollback**: Restore the original etcd.yaml manifest if needed
5. **API server logs**: `kubectl logs -n kube-system kube-apiserver-<node-name>`

✅ **Your etcd backup and restore workflow is complete!**
