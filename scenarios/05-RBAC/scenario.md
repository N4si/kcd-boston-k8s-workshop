# Scenario: RBAC Basics

## Objective

In this scenario, you will practice the fundamentals of Kubernetes RBAC (Role-Based Access Control) by managing permissions for a user and a service account.

## Background

RBAC in Kubernetes allows you to control who can access what resources in your cluster. The key components are:
- **Roles**: Define what actions can be performed on which resources
- **RoleBindings**: Connect roles to users or service accounts
- **ClusterRoles**: Like roles, but cluster-wide
- **ClusterRoleBindings**: Connect cluster roles to users or service accounts

## Tasks

You're working in a cluster where RBAC is enabled. Complete these tasks:

1. **Create a Role** named `sa-creator` in the **default** namespace that allows the creation of service accounts
2. **Create a RoleBinding** named `sa-creator-binding` to bind this role to a user named **Sandra**
3. **Verify permissions** that Sandra can create service accounts in the default namespace using `kubectl auth can-i`
4. **Create a ServiceAccount** named `dev` in the default namespace
5. **Grant permissions** by binding the built-in `view` ClusterRole to the `dev` service account via a RoleBinding
6. **Verify ServiceAccount permissions** that `dev` can list pods and services in the default namespace using `kubectl auth can-i`

## Setup Notes

- Work in the **default** namespace throughout this scenario
- This cluster supports user impersonation with `--as=<username>` and service account impersonation with `--as=system:serviceaccount:<namespace>:<name>`

## Expected Outcome

By completing this scenario, you will:
- Understand how to define roles and bind them to users or service accounts
- Know how to verify permissions using `kubectl auth can-i`
- Understand the difference between users and service accounts in Kubernetes RBAC
- Have hands-on experience with both Role and ClusterRole bindings

## Verification Steps

After each step, verify your work:
```bash
# Verify Sandra's permissions
kubectl auth can-i create serviceaccounts --as=Sandra --namespace=default

# Verify dev service account permissions
kubectl auth can-i list pods --as=system:serviceaccount:default:dev --namespace=default
kubectl auth can-i list services --as=system:serviceaccount:default:dev --namespace=default
```
