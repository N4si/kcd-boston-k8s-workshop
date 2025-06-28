# Solution: Taints and Tolerations

This solution demonstrates how to use taints and tolerations to control pod scheduling. Taints repel pods from nodes, while tolerations allow pods to be scheduled on tainted nodes. This is commonly used for dedicated nodes, maintenance, or special hardware.

## ✅ Step 1: Check the Taint on `node01`

First, examine what taints are applied to the target node:

```bash
# Check taints on node01
kubectl describe node node01 | grep -A 5 -B 5 Taints

# Alternative: Get taints in a more readable format
kubectl get node node01 -o jsonpath='{.spec.taints[*]}' | jq

# Or use this simpler command
kubectl describe node node01 | grep Taints
```

**Expected output**:
```
Taints: dedicated=special-user:NoSchedule
```

**Taint Components Explained**:
- **Key**: `dedicated` - The taint identifier
- **Value**: `special-user` - The taint value
- **Effect**: `NoSchedule` - What happens to pods that don't tolerate this taint

**Taint Effects**:
- `NoSchedule`: Pods won't be scheduled on the node
- `PreferNoSchedule`: Kubernetes will try to avoid scheduling pods on the node
- `NoExecute`: Pods will be evicted from the node if they don't tolerate the taint

---

## ✅ Step 2: Create the Pod Manifest with Toleration

Create or update the pod manifest to include a toleration that matches the node's taint:

```bash
# Create the pod manifest with the required toleration
cat <<EOF > ~/pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    run: nginx
spec:
  # Tolerations allow this pod to be scheduled on tainted nodes
  tolerations:
  - key: "dedicated"           # Must match the taint key exactly
    value: "special-user"      # Must match the taint value exactly
    effect: "NoSchedule"       # Must match the taint effect exactly
    operator: "Equal"          # Default operator (can be omitted)
  
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 80
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
EOF

# Verify the manifest was created correctly
cat ~/pod.yaml
```

**Toleration Explanation**:
- **key, value, effect**: Must exactly match the taint on the node
- **operator**: Can be `Equal` (default) or `Exists`
  - `Equal`: Key, value, and effect must match exactly
  - `Exists`: Only key and effect need to match (ignores value)

**Alternative toleration formats**:
```yaml
# Tolerate any value for the key "dedicated" with NoSchedule effect
tolerations:
- key: "dedicated"
  operator: "Exists"
  effect: "NoSchedule"

# Tolerate all taints on a node (use with caution)
tolerations:
- operator: "Exists"
```

---

## ✅ Step 3: Apply the Pod Manifest

Deploy the pod with the toleration:

```bash
# Apply the pod manifest
kubectl apply -f ~/pod.yaml

# Verify the pod was created
kubectl get pods nginx

# Check the pod's status and events
kubectl describe pod nginx
```

**What to watch for**:
- Pod should transition from `Pending` to `Running`
- Events should show successful scheduling
- No scheduling errors in the events

---

## ✅ Step 4: Verify Pod Scheduling and Status

Confirm the pod is running and scheduled on the correct node:

```bash
# Check pod status and node assignment
kubectl get pods nginx -o wide

# Get detailed information about the pod
kubectl describe pod nginx

# Verify the toleration is applied correctly
kubectl get pod nginx -o jsonpath='{.spec.tolerations}' | jq

# Check that the pod is actually running
kubectl get pod nginx -o jsonpath='{.status.phase}'
```

**Expected output**:
```
NAME    READY   STATUS    RESTARTS   AGE   IP          NODE      NOMINATED NODE   READINESS GATES
nginx   1/1     Running   0          30s   10.x.x.x    node01    <none>           <none>
```

**Success criteria**:
- Pod status should be `Running`
- Node should be `node01`
- No error events in the pod description

---

## ✅ Step 5: Test Without Toleration (Optional)

To understand how taints work, try creating a pod without the toleration:

```bash
# Create a pod without toleration
kubectl run nginx-no-toleration --image=nginx

# Check its status (should remain Pending)
kubectl get pods nginx-no-toleration

# See why it can't be scheduled
kubectl describe pod nginx-no-toleration | grep -A 10 Events
```

**Expected result**: The pod without toleration should remain in `Pending` state with a scheduling error mentioning the taint.

---

## 📚 Understanding Taints and Tolerations

### Common Use Cases

1. **Dedicated Nodes**: Reserve nodes for specific workloads
   ```bash
   kubectl taint nodes node01 dedicated=gpu:NoSchedule
   ```

2. **Maintenance Mode**: Prevent new pods during maintenance
   ```bash
   kubectl taint nodes node01 maintenance=true:NoSchedule
   ```

3. **Node Problems**: Automatically evict pods from problematic nodes
   ```bash
   kubectl taint nodes node01 node.kubernetes.io/unreachable:NoExecute
   ```

### Built-in Taints

Kubernetes automatically applies these taints:
- `node.kubernetes.io/not-ready`: Node is not ready
- `node.kubernetes.io/unreachable`: Node is unreachable
- `node.kubernetes.io/memory-pressure`: Node has memory pressure
- `node.kubernetes.io/disk-pressure`: Node has disk pressure
- `node.kubernetes.io/pid-pressure`: Node has PID pressure

### Toleration vs Node Affinity

- **Tolerations**: Allow pods to be scheduled on tainted nodes (negative selection)
- **Node Affinity**: Attract pods to specific nodes (positive selection)
- **Combined**: Use both for guaranteed placement on specific nodes

Example of guaranteed placement:
```yaml
spec:
  tolerations:
  - key: "dedicated"
    value: "gpu"
    effect: "NoSchedule"
  nodeSelector:
    hardware: "gpu"
```

## 📖 Official Documentation References

- [Taints and Tolerations](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/) - Complete guide
- [Node Affinity](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#node-affinity) - Alternative scheduling method
- [Managing Nodes](https://kubernetes.io/docs/concepts/architecture/nodes/#manual-node-administration) - Node management tasks
- [Pod Scheduling](https://kubernetes.io/docs/concepts/scheduling-eviction/) - Overview of scheduling concepts

## ⚠️ Troubleshooting

### Pod Stuck in Pending State?

1. **Check taints and tolerations**:
   ```bash
   kubectl describe node node01 | grep Taints
   kubectl describe pod nginx | grep -A 5 Tolerations
   ```

2. **Check scheduling events**:
   ```bash
   kubectl describe pod nginx | grep -A 10 Events
   ```

3. **Verify node capacity**:
   ```bash
   kubectl describe node node01 | grep -A 10 "Allocated resources"
   ```

### Common Issues

- **Typos**: Toleration key, value, or effect doesn't match taint exactly
- **Case sensitivity**: Kubernetes is case-sensitive
- **Missing operator**: Default is `Equal`, but `Exists` might be needed
- **Node capacity**: Node might be full even with correct toleration

## 🧠 Key Takeaways

- **Tolerations allow scheduling** on tainted nodes; they don't guarantee it
- **Exact matching required**: Key, value, and effect must match precisely
- **Use with node selectors** for guaranteed placement on specific nodes
- **Built-in taints** handle node conditions automatically
- **NoExecute effect** can evict running pods, not just prevent scheduling

✅ **Your pod is now successfully scheduled on the tainted node!**
