# Workload Resources and Security

### 1. Without creating it, generate the YAML definition for a new ResourceQuota called 'myrq' with hard limits of

- 1 CPU
- 1 Gb memory
- 2 Pods

Store the definition in a file called myrq.yaml.

##### Steps

1. `kubectl create quota myrq --hard=cpu=1,memory=1G,pods=2 -o yaml > myrq.yaml`
2. `vim myrq.yaml`
3. ```yaml
   apiVersion: v1
   kind: ResourceQuota
   metadata:
     creationTimestamp: "2020-12-29T09:38:55Z"
     name: myrq
     namespace: default
     resourceVersion: "1139"
     selfLink: /api/v1/namespaces/default/resourcequotas/myrq
     uid: 5e20d5ab-3df4-47eb-adfc-bb0519558828
   spec:
     hard:
       cpu: "1"
       memory: 1G
       pods: "2"
   status: {}
   ```

---

### 2. Create a Pod called 'nginx' with the 'nginx' image and the following resources

- Requests:
-- CPU: 100m
-- Memory: 256Mi
-- Limits:
- CPU: 200m
-- Memory: 512Mi

##### Steps

1. `kubectl run nginx --image=nginx --dry-run=client -o yaml > nginx-pod.yaml`
2. `vim nginx-pod.yaml`
3. ```yaml
   kind: Pod
   apiVersion: v1
   metadata:
     creationTimestamp: null
     labels:
       run: nginx
     name: nginx
   spec:
     containers:
     - image: nginx
       name: nginx
       resources:
         limits:
           cpu: "200m"
           memory: "512Mi"
         requests:
           cpu: "100m"
           memory: "256Mi"
     dnsPolicy: ClusterFirst
     restartPolicy: Always
   status: {}
   ```
4. `kubectl create -f nginx-pod.yaml`

---

### 3. Create a Pod that

- Is named 'nginx-uid'.
- Uses the 'nginx' image.
- Runs with the user ID 1000.

Make sure that the user ID setting applies to all containers in the Pod

##### Steps

1. `kubectl run nginx-uid --image=nginx --dry-run=client -o yaml > nginx-pod.yaml```
2. `vim nginx-pod.yaml```
3. ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     creationTimestamp: null
     labels:
       run: nginx
     name: nginx-uid
   spec:
     securityContext:
       runAsUser: 101
     containers:
     - image: nginx
       name: nginx-uid
       resources: {}
     dnsPolicy: ClusterFirst
     restartPolicy: Always
   status: {}
   ```
4. `kubectl create -f nginx-pod.yaml`

---

### 4. Create a Pod that

- Is called 'nginx-cap'.
- Uses the 'nginx' image.
- Has the capabilities NET_ADMIN, SYS_TIME added on the 'nginx' container.

##### Steps

1. `kubectl run nginx-cap --image=nginx --dry-run=client -o yaml > nginx-pod.yaml`
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
       securityContext:
         capabilities:
           add: ["NET_ADMIN", "SYS_TIME"]
       resources: {}
     dnsPolicy: ClusterFirst
     restartPolicy: Always
   status: {}
   ```
4. `kubectl create -f nginx-pod.yaml`
