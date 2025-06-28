# Scenario: Network Policy - Restrict Traffic to my-app-deployment Pods

## Context
You are working in a Kubernetes cluster context named `kubernetes-admin@kubernetes`.

There are two deployments already running:
- `my-app-deployment`
- `cache-deployment`

`my-app-deployment` is exposed by a Service called `my-app-service`.

## Objective
Create a NetworkPolicy named `my-app-network-policy` that applies to pods from `my-app-deployment` with the following security rules:

**Ingress (incoming) traffic:**
- Allow traffic from any pod in the cluster
- Additionally allow traffic from pods specifically labeled `app=trusted`
- Deny all other incoming traffic (including external traffic)

**Egress (outgoing) traffic:**
- Allow traffic only to other pods in the cluster
- Deny all external outgoing traffic

This creates a secure pod-to-pod only communication pattern.

## Why?
NetworkPolicies control traffic flow at the pod level, improving security by limiting access only to trusted sources and destinations.

---

## Prerequisites
- `kubectl` configured to use context `kubernetes-admin@kubernetes`
- Deployments and service (`my-app-deployment`, `cache-deployment`, `my-app-service`) already running

---

## Expected Outcome
After applying the NetworkPolicy:
- Pods in `my-app-deployment` accept ingress from any pod in the cluster
- Pods in `my-app-deployment` accept ingress from pods specifically labeled `app=trusted`
- Pods in `my-app-deployment` can send egress traffic only to other pods in the cluster
- All external traffic (both ingress and egress) is blocked for `my-app-deployment` pods

## Verification Steps
Test the NetworkPolicy by:
```bash
# Verify the NetworkPolicy was created
kubectl get networkpolicy my-app-network-policy

# Check which pods the policy applies to
kubectl describe networkpolicy my-app-network-policy

# Test connectivity from a pod with app=trusted label
# Test connectivity from a pod without the label
# Verify external traffic is blocked
```

---

## Additional Resources
For a hands-on interactive lab, try the [KillerKoda Network Policy Scenario](https://killercoda.com/sachin/course/CKA/network-policy)
