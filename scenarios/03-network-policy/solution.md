# Solution: Creating my-app-network-policy

```bash
# Set the correct kubectl context
kubectl config use-context kubernetes-admin@kubernetes

# Create the NetworkPolicy manifest
cat <<EOF | kubectl apply -f -
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: my-app-network-policy
spec:
  # Apply this policy to pods with label app=my-app-deployment
  podSelector:
    matchLabels:
      app: my-app-deployment
  # Define both ingress and egress rules
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from:
    # Allow traffic from any pod in the cluster
    - podSelector: {}
    # Also allow traffic from pods specifically labeled app=trusted
    - podSelector:
        matchLabels:
          app: trusted
  egress:
  - to:
    # Allow egress traffic only to other pods in the cluster
    - podSelector: {}
EOF

# Verify NetworkPolicy is created
kubectl get networkpolicy my-app-network-policy

# Describe the policy to see the details
kubectl describe networkpolicy my-app-network-policy

# Test connectivity (examples)
# kubectl exec -it <test-pod> -- curl <my-app-service>
# kubectl exec -it <trusted-pod> -- curl <my-app-service>
