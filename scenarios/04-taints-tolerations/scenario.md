# Scenario: Taints and Tolerations - Scheduling Pods on Tainted Nodes

## Objective
In this scenario, you will:

- List the taints applied to a node named `node01`.
- Modify a pod manifest to apply a toleration matching the node's taint.
- Successfully schedule the pod onto `node01`.

## Background
Taints and tolerations work together to ensure pods are not scheduled onto inappropriate nodes:

- **Taints** are applied to nodes to repel pods that should not be scheduled there
- **Tolerations** are applied to pods to allow them to schedule on nodes with matching taints
- Common use cases include dedicated nodes for specific workloads, nodes with special hardware, or nodes undergoing maintenance

The taint has three components: key, value, and effect. The toleration must match all three exactly.

---

## Tasks

1. List the taints applied to the node `node01`.
2. Update the pod manifest file (`~/pod.yaml`) to add a toleration that matches the taint on `node01`.
3. Apply the updated pod manifest and verify the pod is scheduled and running on `node01`.

---

## Hints
- Use `kubectl describe node node01` to list taints.
- The toleration key, value, and effect must exactly match the taint.
- Apply the pod manifest with `kubectl apply -f ~/pod.yaml`.
- Verify the pod status and node assignment with `kubectl get pods -o wide`.

---

## Expected Outcome
- The pod successfully schedules on the tainted node `node01`
- The pod status is `Running`
- You understand how taints prevent scheduling and tolerations allow it

## Verification Steps
After applying the pod manifest:
```bash
# Verify the pod is scheduled on node01
kubectl get pods -o wide

# Check the pod is running
kubectl get pods

# Confirm the taint is still present on node01
kubectl describe node node01 | grep Taints
```

## Additional Challenge
Try creating a pod without the toleration and observe that it remains in `Pending` state, unable to schedule on the tainted node.

---

## Additional Resources
For hands-on practice, check out relevant interactive labs or documentation on Kubernetes taints and tolerations.
