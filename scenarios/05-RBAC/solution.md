# Solution: RBAC Basics

Follow the steps below to solve the scenario.

---

## 1. Create a Role to Allow ServiceAccount Creation

```yaml
# sa-creator-role.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: sa-creator
  namespace: default
rules:
- apiGroups: [""]  # Core API group for serviceaccounts
  resources: ["serviceaccounts"]
  verbs: ["create"]  # Only allow creation
```

Apply it:

```bash
kubectl apply -f sa-creator-role.yaml
```

---

## 2. Bind the Role to User "Sandra"

```yaml
# sa-creator-binding.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: sa-creator-binding
  namespace: default
subjects:
- kind: User  # Binding to a user (not a service account)
  name: Sandra
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role  # Reference to the Role we created
  name: sa-creator
  apiGroup: rbac.authorization.k8s.io
```

Apply it:

```bash
kubectl apply -f sa-creator-binding.yaml
```

---

## 3. Verify Permissions for User "Sandra"

Check if Sandra can create service accounts in the default namespace:

```bash
kubectl auth can-i create serviceaccounts --as=Sandra --namespace=default
```

✅ Expected Output: `yes`

---

## 4. Create a Service Account Named `dev`

```bash
kubectl create serviceaccount dev
```

---

## 5. Bind the `view` ClusterRole to the `dev` ServiceAccount

```yaml
# dev-view-binding.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: dev-view-binding
  namespace: default
subjects:
- kind: ServiceAccount  # Binding to a service account
  name: dev
  namespace: default
roleRef:
  kind: ClusterRole  # Using built-in ClusterRole
  name: view  # Built-in role that allows read-only access
  apiGroup: rbac.authorization.k8s.io
```

Apply it:

```bash
kubectl apply -f dev-view-binding.yaml
```

---

## 6. Verify the ServiceAccount's Permissions

Check if the `dev` service account can view pods and services:

```bash
kubectl auth can-i list pods --as=system:serviceaccount:default:dev --namespace=default
kubectl auth can-i list services --as=system:serviceaccount:default:dev --namespace=default
```

✅ Expected Output: `yes` for both.

---

🎉 **Done!** You've successfully:
- Created a custom Role for service account creation
- Bound the role to a user using RoleBinding
- Verified user permissions with `kubectl auth can-i`
- Created a service account and granted it view permissions
- Verified service account permissions

This demonstrates the core RBAC workflow in Kubernetes for both users and service accounts.
