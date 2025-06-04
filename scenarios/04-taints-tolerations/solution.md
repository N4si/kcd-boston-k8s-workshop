# Solution: Taints and Tolerations

---

## ✅ Step 1: Check the taint on `node01`

Use the `kubectl describe` command to find any taints applied:

```bash
kubectl describe node node01 | grep Taints
````

You should see output like:

```
Taints: dedicated=special-user:NoSchedule
```

This tells us that pods can only be scheduled on `node01` if they tolerate the taint `dedicated=special-user` with effect `NoSchedule`.

---

## ✅ Step 2: Update the Pod manifest

Open or create the file `~/pod.yaml` and modify it as follows:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    run: nginx
spec:
  tolerations:
  - key: "dedicated"
    value: "special-user"
    effect: "NoSchedule"
  containers:
  - name: nginx
    image: nginx
```

This toleration matches the taint on `node01` exactly.

---

## ✅ Step 3: Apply the Pod

Apply the manifest using `kubectl`:

```bash
kubectl apply -f ~/pod.yaml
```

---

## ✅ Step 4: Verify pod status and node placement

Check if the pod is running and scheduled to `node01`:

```bash
kubectl get pods -o wide
```

Expected output:

```
NAME    READY   STATUS    RESTARTS   AGE   IP          NODE      ...
nginx   1/1     Running   0          ...   10.x.x.x    node01    ...
```

---

## 🧠 Notes

* Tolerations allow scheduling on tainted nodes; they don't force it.
* For guaranteed placement, use `nodeSelector` or `affinity` along with tolerations if needed.
