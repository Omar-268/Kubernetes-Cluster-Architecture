# Kubernetes-Cluster-Architecture
Welcome to the Kubernetes Cluster Architecture - Visualized repository! 🚀

This repo is designed to help you understand how a Kubernetes cluster works internally — from the moment you deploy a Pod to how networking, scheduling, and control flow happen behind the scenes.

## From kubectl apply to a Running Pod (Clear Step-by-Step)

Below is a breakdown of the full lifecycle of a Pod — showing how each component in the Kubernetes architecture gets involved step-by-step.

## Step 1: User Submits a Pod

 When the user enters ``` kubectl apply -f sample-pod.yaml ```, it means that:

- The user sends a YAML definition of a Pod to the cluster.
- This request is sent to the API server over HTTPS.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-nginx
spec:
  containers:
  - name: nginx
    image: nginx:latest
```

## Step 2: kube-apiserver (Authentication & Validation)

- Validates the manifest.
- Authenticates and authorizes the user.
- If valid, it stores the Pod object as "Pending" in etcd.

## Step 3: etcd (Source of Truth)

- etcd stores all Kubernetes state.
- After validation, the API server persists the Pod:
```jason
{
  "metadata": { "name": "my-nginx" },
  "spec": { ... },
  "status": { "phase": "Pending" }
}
```

  


  


