# KCD Boston Workshop: Hands-On Kubernetes Scenarios

Welcome to the **KCD Boston Workshop** repo! This workshop is built for practical Kubernetes experience. It is **not** focused on certifications but on solving real-world Kubernetes problems and learning how to find answers using the [official Kubernetes documentation](https://kubernetes.io/docs/).

We will use [KillerKoda](https://killerkoda.com/) for hands-on demos. Each folder in the `scenarios/` directory contains one practical scenario, including a description, solution, and optional interactive demo.

---

## 🔧 Workshop Objectives

- Gain practical, hands-on experience with Kubernetes
- Learn how to solve real problems using documentation
- Understand core components like etcd, networking, RBAC, and more
- Build confidence navigating the Kubernetes ecosystem

---

## 📁 Repository Structure

```
kcd-boston-workshop/
├── README.md
├── references/                 # Notes and doc links
│   ├── kubernetes-docs.md
│   └── workshop-resources.md
│
├── scenarios/                 # Each real-world task in a folder
│   ├── 01-version-upgrade/
│   │   ├── scenario.md
│   │   ├── solution.md
│   │   └── killerkoda-link.md
│   ├── 02-etcd-backup/
│   │   ├── scenario.md
│   │   ├── solution.md
│   │   └── killerkoda-link.md
│   ├── 03-network-policy/
│   │   ├── scenario.md
│   │   ├── solution.md
│   │   └── killerkoda-link.md
│   ├── 04-taints-tolerations/
│   │   ├── scenario.md
│   │   ├── solution.md
│   │   └── killerkoda-link.md
│   └── 05-rbac-basics/
│       ├── scenario.md
│       ├── solution.md
│       └── killerkoda-link.md
```

---

## 🧪 Scenarios Overview

| #  | Scenario                | Summary                                              |
|----|-------------------------|------------------------------------------------------|
| 01 | Kubernetes Version Upgrade | Upgrade a running cluster safely                    |
| 02 | ETCD Backup             | Perform and restore a backup of the ETCD datastore  |
| 03 | Network Policy          | Secure pod communication using Kubernetes policies  |
| 04 | Taints & Tolerations    | Control pod placement using taints and tolerations |
| 05 | RBAC Basics             | Create a role and bind it to a user                 |

Each scenario includes:
- A problem description (`scenario.md`)
- A step-by-step solution (`solution.md`)
- A demo link for hands-on testing (`killerkoda-link.md`)

---

## 🔗 Useful Documentation

Check `references/kubernetes-docs.md` for:
- API reference
- Kubectl command guide
- Admin tasks
- Debugging/troubleshooting help

---

## ✅ Getting Started
1. Clone this repo
2. Browse to any scenario folder under `/scenarios`
3. Try the steps in `scenario.md` using your terminal or KillerKoda
4. Cross-check the official docs if you're stuck

Happy learning at KCD Boston! 🎉
