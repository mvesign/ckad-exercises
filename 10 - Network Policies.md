# Network Policies

### 1. Create a deployment that

- Is named 'nginx'
- Runs 2 replicas
- Is exposed via a ClusterIP service named 'nginx' on service port 80
- Has a Pod label with key 'run' and value 'nginx'

Create a NetworkPolicy that:

- Is named 'policy'
- Only allows Pods with label 'access: granted' to access the Deployment Pods (ingress)

TIP: You can create the YAML definition yourself or use the (incomplete) `policy.yaml` file that is provided.

In order to test the NetworkPolicy run the following commands:

- `kubectl run busybox --image=busybox --rm -it --restart=Never -- wget -O- http://nginx:80 --timeout 2`

The above command should fail and timeout after 2 seconds.

- `kubectl run busybox --image=busybox --rm -it --restart=Never --labels=access=granted -- wget -O- http://nginx:80 --timeout 2`

The above command should succeed because of the labels.

##### Steps

1. `kubectl create deployment nginx --image=nginx --dry-run=client -o yaml > deployment.yaml`
2. `vim deployment.yaml`
3. ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     creationTimestamp: null
     labels:
       run: nginx
     name: nginx
   spec:
     replicas: 2
     selector:
       matchLabels:
         run: nginx
     strategy: {}
     template:
       metadata:
         creationTimestamp: null
         labels:
           run: nginx
       spec:
         containers:
         - image: nginx
           name: nginx
           ports:
           - containerPort: 80
   status: {}
   ```
4. `kubectl create -f deployment.yaml`
5. `kubectl expose deployment nginx --type=ClusterIP --port=80 --target-port=80`
6. `vim policy.yaml`
7. ```yaml
   apiVersion: networking.k8s.io/v1
   kind: NetworkPolicy
   metadata:
     name: policy
   spec:
     podSelector:
       matchLabels:
         run: nginx
     ingress:
     - from:
       - podSelector:
           matchLabels:
             access: granted
   ```
8. `kubectl create -f policy.yaml`
