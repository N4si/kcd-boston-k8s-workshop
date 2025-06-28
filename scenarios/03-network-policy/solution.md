# Solution: Creating my-app-network-policy

This solution creates a NetworkPolicy that implements pod-to-pod only communication for the `my-app-deployment` pods. NetworkPolicies are implemented by the Container Network Interface (CNI) plugin and require a CNI that supports NetworkPolicy (like Calico, Cilium, or Weave Net).

## ✅ Step 1: Set the Correct Context

```bash
# Ensure you're using the correct kubectl context
kubectl config use-context kubernetes-admin@kubernetes

# Verify the context
kubectl config current-context
```

**Note**: This step ensures you're working with the correct cluster where the deployments exist.

---

## ✅ Step 2: Create the NetworkPolicy

```bash
# Create the NetworkPolicy manifest and apply it
cat <<EOF | kubectl apply -f -
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: my-app-network-policy
  namespace: default
spec:
  # Apply this policy to pods with label app=my-app-deployment
  podSelector:
    matchLabels:
      app: my-app-deployment
  
  # Define both ingress and egress rules (default deny all)
  policyTypes:
  - Ingress
  - Egress
  
  # Ingress rules: what traffic is allowed INTO the selected pods
  ingress:
  - from:
    # Allow traffic from any pod in the cluster
    - podSelector: {}
    # Note: The above rule allows from ANY pod, including those with app=trusted
    # If you wanted ONLY trusted pods, you would use only the rule below:
    # - podSelector:
    #     matchLabels:
    #       app: trusted
  
  # Egress rules: what traffic is allowed OUT OF the selected pods
  egress:
  - to:
    # Allow egress traffic only to other pods in the cluster
    - podSelector: {}
    # This blocks external traffic (internet, other services outside the cluster)
EOF
```

**NetworkPolicy Explanation**:

- **podSelector**: Selects which pods this policy applies to (pods with `app=my-app-deployment`)
- **policyTypes**: Specifies that we're defining both ingress and egress rules
- **ingress.from.podSelector: {}**: Empty selector means "all pods in the namespace"
- **egress.to.podSelector: {}**: Empty selector means "all pods in the namespace"

**Security Model**: NetworkPolicies are "default deny" - once you apply a policy to pods, all traffic not explicitly allowed is blocked.

---

## ✅ Step 3: Verify the NetworkPolicy

```bash
# Check that the NetworkPolicy was created
kubectl get networkpolicy my-app-network-policy

# Get detailed information about the policy
kubectl describe networkpolicy my-app-network-policy

# Check which pods the policy applies to
kubectl get pods -l app=my-app-deployment --show-labels
```

**Expected output**: You should see the NetworkPolicy listed and the describe command should show the ingress and egress rules.

---

## ✅ Step 4: Test the NetworkPolicy

```bash
# Test connectivity from a pod to my-app-service
# First, get a pod to test from
kubectl get pods

# Test from any pod in the cluster (should work)
kubectl exec -it <any-pod-name> -- curl -m 5 my-app-service:80

# Test external connectivity from my-app pods (should fail)
kubectl exec -it <my-app-pod-name> -- curl -m 5 google.com

# Check if my-app pods can reach other pods (should work)
kubectl exec -it <my-app-pod-name> -- curl -m 5 <other-service>:80
```

**Expected results**:
- ✅ Pod-to-pod communication within the cluster should work
- ❌ External traffic from my-app pods should be blocked
- ✅ Traffic to my-app pods from other cluster pods should work

---

## ✅ Step 5: Advanced Testing (Optional)

```bash
# Create a test pod with app=trusted label
kubectl run trusted-pod --image=busybox --labels="app=trusted" --rm -it -- sh

# From inside the trusted pod, test connectivity
wget -qO- --timeout=5 my-app-service:80

# Create a test pod without the trusted label
kubectl run untrusted-pod --image=busybox --rm -it -- sh

# From inside the untrusted pod, test connectivity (should still work with current policy)
wget -qO- --timeout=5 my-app-service:80
```

---

## 📚 Understanding NetworkPolicy Behavior

### Default Behavior
- **Without NetworkPolicy**: All traffic is allowed
- **With NetworkPolicy**: Only explicitly allowed traffic is permitted (default deny)

### Policy Scope
- NetworkPolicies are **namespace-scoped**
- They apply to pods selected by the `podSelector`
- Multiple policies can apply to the same pod (rules are additive)

### CNI Requirements
NetworkPolicies require a CNI plugin that supports them:
- ✅ **Supported**: Calico, Cilium, Weave Net, Antrea
- ❌ **Not supported**: Flannel (basic), Docker bridge network

## 📖 Official Documentation References

- [Network Policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/) - Complete NetworkPolicy guide
- [NetworkPolicy API Reference](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.28/#networkpolicy-v1-networking-k8s-io) - API specification
- [NetworkPolicy Recipes](https://github.com/ahmetb/kubernetes-network-policy-recipes) - Common NetworkPolicy patterns
- [Securing a Cluster with Network Policies](https://kubernetes.io/docs/tasks/administer-cluster/securing-a-cluster/#network-policies) - Security best practices

## ⚠️ Troubleshooting

### NetworkPolicy Not Working?

1. **Check CNI support**:
   ```bash
   kubectl get nodes -o wide
   # Check what CNI is running
   kubectl get pods -n kube-system | grep -E "(calico|cilium|weave|antrea)"
   ```

2. **Verify policy application**:
   ```bash
   kubectl describe networkpolicy my-app-network-policy
   kubectl get pods -l app=my-app-deployment
   ```

3. **Test with verbose output**:
   ```bash
   kubectl exec -it <pod> -- curl -v my-app-service:80
   ```

4. **Check CNI logs** (example for Calico):
   ```bash
   kubectl logs -n kube-system -l k8s-app=calico-node
   ```

### Common Issues
- **Flannel**: Basic Flannel doesn't support NetworkPolicies
- **DNS**: NetworkPolicies might block DNS resolution - add DNS egress rules if needed
- **Health checks**: Ensure health check traffic is allowed

✅ **Your NetworkPolicy is now active and securing pod-to-pod communication!**
