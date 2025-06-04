# 🎯 Objective

In this scenario, you will practice the fundamentals of Kubernetes RBAC (Role-Based Access Control) by managing permissions for a user and a service account.

---

## 🧩 Scenario

You're working in a cluster where RBAC is enabled. You need to:

1. Create a Role named `sa-creator` in the **default** namespace that allows the creation of service accounts.
2. Bind this role to a user named **Sandra** using a RoleBinding named `sa-creator-binding`.
3. Verify that Sandra can create service accounts in the default namespace using `kubectl auth can-i`.
4. Create a ServiceAccount named `dev` in the default namespace.
5. Grant the built-in `view` ClusterRole to the `dev` service account via a RoleBinding.
6. Verify that `dev` can list pods and services in the default namespace using `kubectl auth can-i`.

---

## 🔧 Setup Notes

- Assume you're working in the **default** namespace.
- This cluster supports user impersonation with `--as=<username>` and service account impersonation with `--as=system:serviceaccount:<namespace>:<name>`.

---

## 🧠 What You'll Learn

- How to define roles and bind them to users or service accounts.
- How to verify permissions using `kubectl auth can-i`.
- Difference between users and service accounts in Kubernetes RBAC.
