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

---

## 📚 Understanding RBAC Components

### RBAC Building Blocks

1. **Role**: Defines permissions within a namespace
2. **ClusterRole**: Defines permissions cluster-wide
3. **RoleBinding**: Grants Role permissions to users/groups/service accounts in a namespace
4. **ClusterRoleBinding**: Grants ClusterRole permissions cluster-wide

### Permission Model

RBAC uses a "deny by default" model:
- No permissions are granted by default
- All permissions must be explicitly granted
- Permissions are additive (multiple roles can be bound to the same subject)

### Built-in ClusterRoles

Kubernetes provides several built-in ClusterRoles:
- `cluster-admin`: Full cluster access
- `admin`: Full namespace access
- `edit`: Read/write access to most resources in a namespace
- `view`: Read-only access to most resources in a namespace

## 📖 Official Documentation References

- [RBAC Authorization](https://kubernetes.io/docs/reference/access-authn-authz/rbac/) - Complete RBAC guide
- [Using RBAC Authorization](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#command-line-utilities) - Practical examples
- [Service Accounts](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/) - Service account management
- [Authentication Overview](https://kubernetes.io/docs/reference/access-authn-authz/authentication/) - Authentication methods
- [Authorization Overview](https://kubernetes.io/docs/reference/access-authn-authz/authorization/) - Authorization modes
- [kubectl auth can-i](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#auth) - Permission testing

## ⚠️ Troubleshooting RBAC

### Permission Denied Errors?

1. **Check current permissions**:
   ```bash
   kubectl auth can-i --list --as=Sandra
   kubectl auth can-i --list --as=system:serviceaccount:default:dev
   ```

2. **Verify role bindings**:
   ```bash
   kubectl get rolebindings -o wide
   kubectl describe rolebinding sa-creator-binding
   ```

3. **Check role definitions**:
   ```bash
   kubectl describe role sa-creator
   kubectl describe clusterrole view
   ```

### Common Issues

- **Wrong namespace**: RoleBindings only work in their namespace
- **Typos**: Subject names must match exactly
- **API groups**: Ensure correct apiGroups in role rules
- **Verbs**: Check that the required verbs are included

### Useful Debugging Commands

```bash
# List all roles and bindings
kubectl get roles,rolebindings,clusterroles,clusterrolebindings

# Check what a user can do
kubectl auth can-i --list --as=Sandra --namespace=default

# Test specific permissions
kubectl auth can-i create pods --as=Sandra --namespace=default
kubectl auth can-i get secrets --as=system:serviceaccount:default:dev

# View effective permissions
kubectl describe rolebinding --all-namespaces
```
