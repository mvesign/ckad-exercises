# Pod Management

### 1. Change the image for Pod 'nginx' to `nginx:1.7.1`. Observe that the pod will be killed and recreated as soon as the image gets pulled

Run the following command to retrieve Pod information and check if you can find the current image for this Pod.

- `kubectl describe pod nginx`

Run the following command to check the Pod logs:

- `kubectl logs nginx`

If the Pod has crashed, you can view the logs for the previous Pod with.

- `kubectl logs -p nginx`

##### Steps

1. `kubectl set image pod/nginx nginx=nginx:1.7.1`
2. `kubectl describe pod nginx`
3. `kubectl logs nginx`
4. `kubectl logs -p nginx`

---

### 2. Create a Pod that

- Is named 'busybox'.
- Uses the image 'busybox'.
- Has two containers that use image 'busybox' and run with arguments 'sleep' and '3600'.

##### Steps

1. `kubectl run busybox --image=busybox --dry-run=client -o yaml > busybox-pod.yaml`
2. `vim busybox-pod.yaml`
3. ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     creationTimestamp: null
     labels:
       run: busybox
     name: busybox
   spec:
     containers:
     - name: busybox1
       image: busybox
       args: ["sleep", "3600"]
     - name: busybox2
       image: busybox
       args: ["sleep", "3600"]
     dnsPolicy: ClusterFirst
     restartPolicy: Always
   status: {}
   ```
4. `kubectl create -f busybox-pod.yaml`
5. `kubectl describe pod busybox`
6. `kubectl exec -it busybox -c busybox1 -- ls`
7. `kubectl logs busybox -c busybox1`
8. `kubectl exec -it busybox -c busybox2 -- ls`
9. `kubectl logs busybox -c busybox2`

---

### 3. Create a Pod that

- Is named 'busybox'
- Uses the image 'busybox'
- Passes 'ls' and '/notexists' as arguments to the container

Run the following command to see (error) events for the Pod.

- `kubectl describe pod busybox`

Run the following command to check the Pod logs.

- `kubectl logs busybox`

Run the following command to forcefully delete the Pod.

- `kubectl delete pod busybox --now`

##### Steps

1. `kubectl run busybox --image=busybox --dry-run=client -o yaml > busybox-pod.yaml`
2. `vim busybox-pod.yaml`
3. ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     creationTimestamp: null
     labels:
       run: busybox
     name: busybox
   spec:
     containers:
     - image: busybox
       name: busybox
       command: ["/bin/sh"]
     args: ["ls", "/notexist"]
     dnsPolicy: ClusterFirst
     restartPolicy: Always
   status: {}
   ```
4. `kubectl create -f busybox-pod.yaml`
5. `kubectl describe pod busybox`
6. `kubectl logs busybox`
7. `kubectl delete pod busybox --now`
