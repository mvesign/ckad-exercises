# Kubernetes API Access

### 1. Create a ServiceAccount named 'myuser'

TIP: You can check that the ServiceAccount exists with:

- `kubectl get serviceaccount --all-namespaces`

This will list all the ServiceAccount's in the cluster, including the one you created.

##### Steps

1. `kubectl create serviceaccount myuser`
2. `kubectl get serviceaccount --all-namespaces`
3. `kubectl get serviceaccount myuser`

---

### 2. Create a Pod that

- Is named 'nginx'
- Uses the image 'nginx'
- Uses the 'myuser' ServiceAcount

##### Steps

1. `kubectl run nginx --image=nginx --dry-run=client --serviceaccount=myuser -o yaml > nginx-pod.yaml`
2. `vim nginx-pod.yaml`
3. ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     creationTimestamp: null
     labels:
       run: nginx
     name: nginx
   spec:
     containers:
     - image: nginx
       name: nginx
       resources: {}
     dnsPolicy: ClusterFirst
     restartPolicy: Always
     serviceAccountName: myuser
   status: {}
   ```
4. `kubectl create -f nginx-pod.yaml`
5. `kubectl describe pod nginx`
