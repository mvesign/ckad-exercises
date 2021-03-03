# Resource Metadata

### 1. Create three Pods named 'nginx1', 'nginx2' and 'nginx3'

Make sure that each Pod:

- Has a label with key 'app' and value 'v1'.

##### Steps

1. `kubectl run nginx1 --image=nginx --labels="app=v1"`
2. `kubectl run nginx2 --image=nginx --labels="app=v1"`
3. `kubectl run nginx3 --image=nginx --labels="app=v1"`

---

### 2. Run the following commands to inspect the Pod labels

- `kubectl get pods --show-labels`
- `kubectl get pods --label-columns app`
- `kubectl describe pods`

The 'describe' command will output information for all Pods. Try to locate the label information in the output for each Pod.

##### Steps

1. `kubectl get pods --show-labels`
2. `kubectl get pods --label-columns app`
3. `kubectl describe pods`

---

### 3. Change the label of Pod 'nginx2' from 'app=v1' to 'app=v2'

##### Steps

1. `kubectl label pods nginx2 app=v2 --overwrite`
2. `kubectl get pods --show-labels`

---

### 4. Remove the 'app' label from all nginx Pods

##### Steps

1. `kubectl label pods --all app-`

---

### 5. Create a Pod that will only be deployed to a Node with label `accelerator=nvidia-tesla-p100`

In this assignment we have provided Pod definition in a file called `pod.yaml`. Adjust this file to make sure that the Pod is only scheduled on nodes with the above mentioned label.

##### Steps

1. `vim pod.yaml`
2. ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: cuda-test
   spec:
     containers:
     - name: cuda-test
       image: "k8s.gcr.io/cuda-vector-add:v0.1"
     nodeSelector:
       accelerator: nvidia-tesla-p100
   ```
3. `kubectl create -f pod.yaml`

---

### 6. Annotate Pods nginx1, nginx2 and nginx3 with key `description` and value `my annotation`

##### Steps

1. `kubectl annotate pods nginx1 nginx2 nginx3 description="my annotation"`

---

### 7. Run the following commands to inspect the Pod annotations

- `kubectl get pods -o jsonpath='{range .items*}{@.metadata.name}:{@.metadata.annotations}{"\n"}'`
- `kubectl describe pods`
- `kubectl describe pods nginx1 | grep Annotations`

The 'describe' command will output information for all Pods. Try to locate the annotations information in the output for each Pod.

##### Steps

1. `kubectl get pods -o jsonpath='{range .items*}{@.metadata.name}:{@.metadata.annotations}{"\n"}'`
2. `kubectl describe pods`

---

### 8. Remove the 'description' annotations for all Nginx Pods

##### Steps

1. `kubectl annotate pods nginx1 nginx2 nginx3 description-`
