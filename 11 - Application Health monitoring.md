# Application Health monitoring

### 1. Create a Pod

- That is named 'nginx'
- Runs the image 'nginx'
- Contains a liveness probe that runs the command 'ls'

##### Steps

1. `kubectl run nginx --image=nginx --dry-run=client -o yaml > nginx-pod.yaml`
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
       livenessProbe:
         exec:
           command:
           - ls
       resources: {}
     dnsPolicy: ClusterFirst
     restartPolicy: Always
   status: {}
   ```
4. `kubecl create -f nginx-pod.yaml`
5. `kubectl describe pod nginx`

---

### 2. Use the Pod from the previous assignment but make sure that

- The liveness probe starts kicking in after 5 seconds whereas the interval between probes would be 5 seconds.

TIP: There is an existing pod.yaml that you can update.

##### Steps

1. `vim nginx-pod.yaml`
2. ```yaml
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
       livenessProbe:
         exec:
           command:
           - ls
         initialDelaySeconds: 5
         periodSeconds: 5
       resources: {}
     dnsPolicy: ClusterFirst
     restartPolicy: Always
   status: {}
   ```
3. `kubectl create -f nginx-pod.yaml`
4. `kubectl describe pod nginx`

---

### 3. Create a Pod that

- Is named 'nginx'
- Uses the image 'nginx'
- Configures port 80 on the container
- Runs an HTTP Readiness probe on path '/'

##### Steps

1. `kubectl run nginx --image=nginx --dry-run=client --port=80 -o yaml > nginx-pod.yaml`
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
       ports:
       - containerPort: 80
       readinessProbe:
         httpGet:
           path: /
           port: 80
       resources: {}
     dnsPolicy: ClusterFirst
     restartPolicy: Always
   status: {}
   ```
4. `kubectl create -f nginx-pod.yaml`
5. `kubectl describe pod nginx`

---

### 4. There are three Pods running. Determine which one is using the most CPU and write the name of the Pod to a file called most.cpu

NOTE: For now, the challenge check always succeeds. We are working on implementing this challenge properly. Do try to solve the exercise as if the check was enabled.

##### Steps

1. `kubectl top pod --sort-by='cpu' > most.cpu`
