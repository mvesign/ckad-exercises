# Application exposure

### 1. Create a Pod that

- Is named 'nginx'.
- Uses the 'nginx' image.
- Allows traffic on port 80.

##### Steps

1. `kubectl run nginx --image=nginx --port=80`
2. `kubectl get pods nginx -o yaml > nginx-pod.yaml`
3. `vim nginx-pod.yaml`
4. ```yaml
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
       resources: {}
     dnsPolicy: ClusterFirst
     restartPolicy: Never
   status: {}
   ```

---

### 2. Create a Service for the 'nginx' Pod that

- Is of type 'ClusterIP'
- Exposes the 'nginx' container port 80 on ClusterIP port 80

Verify that your service was created successfully by running:

- `kubectl get service nginx`

Verify that your service contains endpoints:

- `kubectl get endpoints nginx`

The endpoint should correspond the the Pod IP. Find the 'nginx' Pod IP and check that it matches the endpoint.

##### Steps

1. `kubectl expose pod nginx --type=ClusterIP --port=80 --target-port=80`
2. `kubectl get service nginx`
3. `kubectl get service nginx`
4. `kubectl describe pod nginx`

---

### 3. Find the IP address for the 'nginx' ClusterIP service

Run the following command to start a temporary 'busybox' Pod and perform a HTTP GET request to the service. The response will be stored in a file called 'response.txt' (replace $CLUSTER_IP with the IP address that you found for the ClusterIP service):

- `kubectl run -it --rm --restart=Never --image=busybox busybox -- /bin/sh -c 'wget $CLUSTER_IP && cat index.html' > response.txt`

Inspect the contents of the response.txt file to confirm that you got a response from Nginx.

##### Steps

1. `kubectl describe service nginx`
2. `kubectl run -it --rm --restart=Never --image=busybox busybox -- /bin/sh -c 'wget 10.43.107.203 && cat index.html' > response.txt`
3. `cat response.txt`

---

### 4. Convert the ClusterIP to NodePort for the same service and find the NodePort port

Hit the service with 'curl' using the Node IP and NodePort port:

- `curl $NODE_IP:$NODE_PORT`

You should get a nice nginx HTML response.

##### Steps

1. `kubectl edit service nginx -o yaml`
2. `kubectl describe service nginx`
3. `kubectl describe pod nginx`
4. `curl 10.132.0.33:31342`

---

### 5. Create a Deployment that

- Is named 'foo'
- Uses the image 'dgkanatsios/simpleapp'
- Runs three replicas
- Has a Pod label with key 'app' and value 'foo'
- Runs containers that accept traffic on port 8080

##### Steps

1. `kubectl create deployment foo --image=dgkanatsios/simpleapp --dry-run=client -o yaml > deployment.yaml`
2. `vim deployment`
3. ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     creationTimestamp: null
     labels:
       app: foo
     name: foo
   spec:
     replicas: 3
     selector:
       matchLabels:
         app: foo
     strategy: {}
     template:
       metadata:
         creationTimestamp: null
         labels:
           app: foo
       spec:
         containers:
         - image: dgkanatsios/simpleapp
           name: simpleapp
           ports:
       - containerPort: 8080
   status: {}
   ```
4. `kubectl create -f deployment.yaml`

---

### 6. Create a service that

- Is of type 'NodePort'
- Exposes the deployment 'foo' on NodePort 31999.

Hit the service with 'curl' using the Node IP and NodePort port:

- `curl $NODE_IP:31999`

You should see a different response every time, indicating that loadbalancing is happening.

##### Steps

1. `kubectl expose deployment foo --type=NodePort`
2. `kubectl edit deployment foo`
3. `kubectl describe pod foo`
4. `curl 10.132.0.33:31999`
5. `curl 10.132.0.33:31999`
6. `curl 10.132.0.33:31999`
