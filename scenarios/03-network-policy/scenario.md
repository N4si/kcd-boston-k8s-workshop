# Scenario: Network Policy - Restrict Traffic to my-app-deployment Pods

## Context
You are working in a Kubernetes cluster context named `kubernetes-admin@kubernetes`.

There are two deployments already running:
- `my-app-deployment`
- `cache-deployment`

`my-app-deployment` is exposed by a Service called `my-app-service`.

## Objective
Create a NetworkPolicy named `my-app-network-policy` with the following rules:

- Allow incoming traffic **only from pods**.
- Allow incoming traffic **only from pods with label** `app=trusted`.
- Allow outgoing traffic **only to pods**.
- Deny all other incoming and outgoing traffic to `my-app-deployment` pods.

## Why?
NetworkPolicies control traffic flow at the pod level, improving security by limiting access only to trusted sources and destinations.

---

## Prerequisites
- `kubectl` configured to use context `kubernetes-admin@kubernetes`
- Deployments and service (`my-app-deployment`, `cache-deployment`, `my-app-service`) already running

---

## Expected Outcome
After applying the NetworkPolicy:
- Pods in `my-app-deployment` accept ingress only from pods labeled `app=trusted`
- Pods in `my-app-deployment` can send egress traffic only to other pods
- All other ingress and egress traffic is blocked for `my-app-deployment` pods

---

## Additional Resources
For a hands-on interactive lab, try the [KillerKoda Network Policy Scenario](https://killercoda.com/sachin/course/CKA/network-policy)
