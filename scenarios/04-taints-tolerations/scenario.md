# Scenario: Taints and Tolerations - Scheduling Pods on Tainted Nodes

## Objective
In this scenario, you will:

- List the taints applied to a node named `node01`.
- Modify a pod manifest to apply a toleration matching the node's taint.
- Successfully schedule the pod onto `node01`.

## Background
Nodes can be tainted to repel pods that do not tolerate the taint. To allow a pod to schedule on a tainted node, the pod must have a matching toleration.

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
- The pod successfully schedules on the tainted node `node01`.
- The pod status is `Running`.

---

## Additional Resources
For hands-on practice, check out relevant interactive labs or documentation on Kubernetes taints and tolerations.
