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
  podSelector:
    matchLabels:
      app: my-app-deployment
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from:
    - podSelector: {}            # Allow from any pod
    - podSelector:
        matchLabels:
          app: trusted           # Allow from pods with label app=trusted
  egress:
  - to:
    - podSelector: {}            # Allow egress to any pod
EOF

# Verify NetworkPolicy is created
kubectl get networkpolicy my-app-network-policy

# Test by checking pods labels and connectivity accordingly
