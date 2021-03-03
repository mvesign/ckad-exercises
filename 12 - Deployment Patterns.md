# Deployment Patterns

### 1. Create a deployment that

- Is named 'nginx'
- Uses image nginx:1.7.8
- Has two 2 replicas,
- Defines port 80 as the container port

##### Steps

1. `kubectl create deployment nginx --image=nginx:1.7.8 --dry-run=client -o yaml > nginx-deployment.yaml`
2. `vim nginx-deployment.yaml`
3. ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     creationTimestamp: null
     labels:
       app: nginx
     name: nginx
   spec:
     replicas: 2
     selector:
       matchLabels:
         app: nginx
     strategy: {}
     template:
       metadata:
         creationTimestamp: null
         labels:
           app: nginx
       spec:
         containers:
         - image: nginx:1.7.8
           name: nginx
           ports:
           - containerPort: 80
           resources: {}
   status: {}
   ```
4. `kubectl create -f nginx-deployment.yaml`
5. `kubectl describe deployment nginx`

---

### 2. Find the names of the ReplicaSets that were created by the previous 'nginx' Deployment and store them in a file called rs.txt

TIP: There is another deployment running, which also created ReplicaSets. Make sure that you put the correct ReplicaSet names in the file (those belonging to the 'nginx' Deployment).

##### Steps

1. `kubectl get replicasets --show-labels`
2. `kubectl get replicasets -l app=nginx > rs.txt`
3. `cat rs.txt`

---

### 3. Check the rollout status for the 'nginx' Deployment

##### Steps

1. `kubectl describe deployment nginx`
2. `kubectl rollout status deployment/nginx`

---

### 4. Update the image for Deployment 'nginx' to `nginx:1.7.9`

##### Steps

1. `kubectl set image deployment/nginx nginx=nginx:1.7.9 --record`
2. `kubectl describe deployment nginx`

### 5. Check the rollout history for the 'nginx' Deployment. Confirm that the replicas are correct with the following commands

- `kubectl get deployment nginx`

After running the above command you should see a 'Ready' status of 2/2

- `kubectl get replicaset`

After running the above command you should see two ReplicaSets. One of them will have the 'Desired', 'Current', and 'Ready' amount set to 2.

- `kubectl get pod`

After running the above command you should see two 'nginx-' Pods that belong to them Deployment and ReplicaSet.

##### Steps

1. `kubectl rollout history deployment/nginx`
2. `kubectl get deployment nginx`
3. `kubectl get replicaset`
4. `kubectl get pod`

---

### 6. Undo the latest rollout for Deployment 'nginx' and verify that new pods have the old image (nginx:1.7.8) with

- `kubectl get pod -l app=nginx -o jsonpath='{.items[*].spec.containers[*].image}'`

##### Steps

1. `kubectl rollout undo deployment/nginx`
2. `kubectl get pod -l app=nginx -o jsonpath='{.items[*].spec.containers[*].image}'`
3. `kubectl describe deployment nginx`

---

### 7. Set the image for Deployment 'nginx' to 'nginx:1.99'. Check the rollout status with

- `kubectl rollout status deploy/nginx`

You will find that the Deployment rollout is not making any progress. Abort the rollout status with ctrl-c.

Bonus (not required for the check): Can you find the reason why the Deployment is not succeeding?

##### Steps

1. `kubectl set image deployment/nginx nginx=nginx:1.99 --record`
2. `kubectl rollout status deployment/nginx`
3. CTRL + C

BONUS: No nginx instance present for version 1.99

---

### 8. You can inspect rollout revision details for Deployment 'nginx' with

- `kubectl rollout history deploy/nginx --revision=2`

Rollback Deployment 'nginx' to the second revision (number 2) and verify that the image is again nginx:1.7.9 with:

- `kubectl get pod -l app=nginx -o jsonpath='{.items[*].spec.containers[*].image}'`

##### Steps

1. `kubectl rollout history deployment/nginx --revision=2`
2. `kubectl rollout undo deployment/nginx --to-revision=2`
3. `kubectl get pod -l app=nginx -o jsonpath='{.items[*].spec.containers[*].image}'`
4. `kubectl describe deployment nginx`

---

### 9. Scale the 'nginx' Deployment to 5 replicas

##### Steps

1. `kubectl scale deployment/nginx --replicas=5`
2. `kubectl describe deployment nginx`

---

### 10. Create a horizontal pod autoscaler that

- Autoscales the Deployment 'nginx'
- Allows the number of Pods to be between 5 and 10
- Starts scaling with a CPU utilization threshold of 80%

##### Steps

1. `kubectl autoscale deployment nginx --min=5 --max=10 --cpu-percent=80 --dry-run=client -o yaml > autoscale.yaml`
2. `vim autoscale.yaml`
3. ```yaml
   apiVersion: autoscaling/v1
   kind: HorizontalPodAutoscaler
   metadata:
     creationTimestamp: null
     name: nginx
   spec:
     maxReplicas: 10
     minReplicas: 5
     scaleTargetRef:
       apiVersion: apps/v1
       kind: Deployment
       name: nginx
     targetCPUUtilizationPercentage: 80
   status:
     currentReplicas: 0
     desiredReplicas: 0
   ```
4. `kubectl create -f autoscale.yaml`
5. `kubectl describe horizontalpodautoscaler.autoscaling/nginx`

---

### 11. Pause the Deployment 'nginx'

##### Steps

1. `kubectl rollout pause deployment/nginx`

---

### 12. Update the image for Deployment 'nginx' to `nginx:1.9.1`

Check that the rollout is not starting with:

- `kubectl rollout status deploy/nginx`

The Deployment is in a paused state and will not proceed until it is unpaused.

##### Steps

1. `kubectl set image deployment/nginx nginx=nginx:1.9.1 --record`
2. `kubectl rollout status deploy/nginx`
3. `kubectl describe deployment nginx`

---

### 13. Resume the rollout for Deployment 'nginx'

Check that the rollout is proceeding with:

- `kubectl rollout status deploy/nginx`

##### Steps

1. `kubectl rollout resume deployment/nginx`
2. `kubectl rollout status deployment/nginx`
