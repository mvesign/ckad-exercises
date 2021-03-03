# Configuration management

### 1. Create a Pod that

- Is named 'nginx'.
- Uses the 'nginx' image.
- Has an environment variable with key 'var1' and value 'val1'.

Check that the environment variable exists within the Pod by running `kubectl exec -it nginx -- env`

##### Steps

1. `kubectl run nginx --image=nginx --env="var1=val1"`
2. `kubectl exec -it nginx -- env`

---

### 2. Create a ConfigMap that

- Is named 'config'.
- Contains key/value data 'foo=lala' and 'foo2=lolo'

Beforing submitting you solution, inspect the ConfigMap data by running either.

- `kubectl describe configmap config`
- `kubectl get configmap config -oyaml`

##### Steps

1. `kubectl create configmap config --from-literal="foo=lala" --from-literal="foo2=lolo"`
2. `kubectl describe configmap config`
3. `kubectl get configmap config -oyaml`

---

### 3. Create a ConfigMap from a file that

- Is named 'cmfile'.
- Uses the contents of file 'config.txt' as data.

##### Steps

1. `kubectl create configmap cmfile --from-file=config.txt`
2. `kubectl describe configmap cmfile`

---

### 4. Create a ConfigMap that

- Is named 'cmspecial'.
- Uses the contents of file 'special.txt' as data.
- Uses the key 'special' to access the file contents in the data.

##### Steps

1. `kubectl create configmap cmspecial --from-file=special=special.txt`
2. `kubectl describe configmap cmspecial`

---

### 5. Create a ConfigMap that

- Is named 'options'.
- Has 'var5=val5' as data.

Create a Pod that.

- Is named 'nginx'.
- Uses the 'nginx' image.
- Uses the ConfigMap 'options' to set an environment variable within the container called 'OPTION' based on the single key/value pair 'var5' in the ConfigMap.

##### Steps

1. `kubectl create configmap options --from-literal=var5=val5`
2. `kubectl run nginx --image=nginx --dry-run=client -o yaml > nginx-pod.yaml`
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
       env:
       - name: OPTION
         valueFrom:
           configMapKeyRef:
             name: options
             key: var5
       resources: {}
     dnsPolicy: ClusterFirst
     restartPolicy: Always
   status: {}
   ```
5. `kubectl create -f nginx-pod.yaml`
6. `kubectl exec nginx -- env`

---

### 6. Create a ConfigMap that

- Is named 'anotherone'.
- Has 'var6=val6' and 'var7=val7' as data.

Create a Pod that.

- Is named 'nginx'.
- Uses the 'nginx' image
- Uses the ConfigMap 'anotherone' to set environment variables within the container based on all key/value pairs in the ConfigMap

##### Steps

1. `kubectl create configmap anotherone --from-literal="var6=val6" --from-literal="var7=val7"`
2. `kubectl run nginx --image=nginx --dry-run=client -o yaml > nginx-pod.yaml`
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
       envFrom:
       - configMapRef:
           name: anotherone
       resources: {}
     dnsPolicy: ClusterFirst
     restartPolicy: Always
   status: {}
   ```
5. `kubectl create -f nginx-pod.yaml`
6. `kubectl exec nginx -- env`

---

### 7. Create a configMap that

- Is named 'cmvolume'.
- Contains 'var8=val8' and 'var9=val9' as data.

Create a Pod that.

- Is name 'nginx'.
- Uses the 'nginx' image.
- Attaches the ConfigMap 'cmvolume' as volume called 'myvolume' to the Pod and mounts it at path '/etc/lala'.

##### Steps

1. `kubectl create configmap cmvolume --from-literal=var8=val8 --from-literal=var9=val9`
2. `kubectl run nginx --image=nginx --dry-run=client -o yaml > nginx-pod.yaml`
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
       volumeMounts:
       - name: myvolume
         mountPath: /etc/lala
     volumes:
       - name: myvolume
         configMap:
           name: cmvolume
     dnsPolicy: ClusterFirst
     restartPolicy: Always
   status: {}
   ```
5. `kubectl create -f nginx-pod.yaml`
6. `kubectl exec nginx -- ls /etc/lala`

---

### 8. Create a Secret that

- Is named 'mysecret'.
- Contains key/value data 'password=mypass'.

##### Steps

1. `kubectl create secret generic mysecret --from-literal=password=mypass`

---

### 9. Create a Secret that

- Is named 'mysecret2'.
- Uses the contents of file 'username' as data.

After creating the data you can view the secrets in plain-text with the following commands.

- `kubectl get secret mysecret2 -oyaml`
-- find the Base64 encoded $VALUE for username
- `echo $VALUE | base64 --decode`

##### Steps

1. `kubectl create secret generic mysecret2 --from-file=username`
2. `kubectl get secret mysecret2 -o yaml`
3. `kubectl get secret mysecret2 -o jsonpath="{.data.username}" | base64 --decode`

---

### 10. Create a Pod that

- Is named 'nginx'.
- Uses the 'nginx' image.
- Attaches the Secret 'mysecret2' as volume to the Pod and mounts it at path '/etc/foo'.

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
       volumeMounts:
       - name: myvolume
         mountPath: /etc/foo
     volumes:
       - name: myvolume
         secret:
           secretName: mysecret2
     dnsPolicy: ClusterFirst
     restartPolicy: Always
   status: {}
   ```
4. `kubectl create -f nginx-pod.yaml`
5. `kubectl exec -it nginx -- ls /etc/foo`
