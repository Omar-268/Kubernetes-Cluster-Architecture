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

## Step 4: kube-scheduler (Assigns Node)

- When a Pod is first created, the ```spec.nodeName``` field is null, which signals to Kubernetes that this Pod needs to be scheduled

- The kube-scheduler watches for such unassigned Pods and selects the most appropriate Node based on:
 - CPU/RAM
 - Affinities
 - Taints/tolerations
 - Node health
- Updates the Pod spec with the selected node:

### Example - Taints and Tolerations
If a Node has a taint:
```kubectl taint nodes node1 key=value:NoSchedule```
Then a Pod must include a matching toleration:
```spec:
  tolerations:
  - key: "key"
    operator: "Equal"
    value: "value"
    effect: "NoSchedule"
```
Otherwise, the Pod won’t be assigned to that Node.

After a Node is selected, the kube-scheduler binds the Pod to it 

## Step 5: kube-controller-manager 

- Watches for state changes via the API server.
- Ensures the Pod is actually created on the target Node.
- If the Pod is part of a ReplicaSet/Deployment, ensures required replicas exist.

## Step 6: kubelet 

- Runs on every Worker Node.
- Watches the API server for Pods assigned to its Node.
- Once a Pod is scheduled to the node:
  - Pulls the container image (e.g. nginx:latest).
  - Sends instructions to the container runtime to create and run the container.
  - Monitors container health via liveness and readiness probes.
  - Periodically reports Pod status (e.g. "Running", "Failed") back to the API server.
 
## References

[Kubernetes Official Docs](https://kubernetes.io/docs/concepts/architecture/)



  


