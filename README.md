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
│   └── 05-RBAC/
│       ├── scenario.md
│       ├── solution.md
│       └── killerkoda-link.md
```

---

## 🧪 Scenarios Overview

| #  | Scenario                | Summary                                              |
|----|-------------------------|------------------------------------------------------|
| 01 | Kubernetes Version Upgrade | Upgrade a running cluster safely from 1.31.x to 1.32.x |
| 02 | ETCD Backup             | Perform and restore a backup of the ETCD datastore  |
| 03 | Network Policy          | Secure pod communication using Kubernetes policies  |
| 04 | Taints & Tolerations    | Control pod placement using taints and tolerations |
| 05 | RBAC Basics             | Create roles and bind them to users and service accounts |

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
3. Read the `scenario.md` to understand the problem and requirements
4. Try to solve it yourself using the official Kubernetes documentation
5. Check your solution against `solution.md` when ready
6. Use the KillerKoda links for hands-on practice in a real environment

## 🧪 Testing Your Solutions
Each scenario includes verification steps to confirm your solution works correctly. Always test your commands and YAML manifests before considering a scenario complete.

Happy learning at KCD Boston! 🎉
