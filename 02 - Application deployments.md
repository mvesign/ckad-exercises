# Application deployments

### 1. Create a Pod in the Namespace `mynamespace` using the YAML that is provided in a file called `pod.yaml`

##### Steps

1. `kubectl create --namespace=mynamespace -f pod.yaml`

---

### 2. Get the YAML definition for the pod that you created in the previous assignment

##### Steps

1. `kubectl get pod nginx --namespace=mynamespace -o yaml`

---

### 3. Create a busybox Pod named 'busybox' with the `kubectl run` command that runs the command `env`

Make sure that you specify flags which:

- Never allow the pod to be restarted.
- Run the Pod interactively.
- Specify 'env' as a command to the Pod and not as an argument.
- Specify 'busybox' as the image.

Finally, retrieve the logs from this pod.

##### Steps

1. `kubectl run -it busybox --image=busybox --restart=Never --command -- env`
2. `kubectl logs busybox`

---

### 4. Create a busybox Pod named 'busybox' with the `kubectl apply` command from a YAML definition. The pod needs to execute the command `env`

Make sure that you:

- Never allow the pod to be restarted.
- Specify 'busybox' as the image.
- Specify 'env' as a command to the Pod and not as an argument.

You need to generate the YAML for the Pod. Use the command `kubectl run` with the option to output the YAML definition. Save this YAML definition to a file and use it to apply the Pod resource.

Finally, retrieve the logs from this pod

##### Steps

1. `kubectl run busybox --image=busybox --restart=Never --dry-run=client -o yaml > busybox-pod.yaml`
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
       command: ["env"]
       resources: {}
     dnsPolicy: ClusterFirst
     restartPolicy: Never
   status: {}
   ```
4. `kubectl apply -f busybox-pod.yaml`
5. `kubectl logs busybox`

---

### 5. Use `kubectl get` to retrieve Pods in all namespaces

##### Steps

1. `kubectl get pods --all-namespaces`

---

### 6. Use the `kubectl exec` command to execute an interactive shell (`/bin/sh`) on the Nginx pod

##### Steps

1. `kubectl exec -it nginx -- /bin/sh`
2. `exit`

---

### 7. Create a Busybox Pod that runs echo 'hello world' and then exits.

The default command for the Busybox container is '/bin/sh'. Make sure that you pass the 'echo' command as arguments to the container. Furthermore, make sure to:

- Never allow the pod to be restarted.
- Specify 'busybox' as the image.

##### Steps

1. `kubectl run -it --image=busybox busybox --restart=Never -- echo 'hello world'`

---

### 8. Create a Busybox Pod that runs echo 'hello world' and then exits and deletes itself

The default command for the Busybox container is '/bin/sh'. Make sure that you pass the 'echo' command as arguments to the container. Furthermore, make sure to:

- Never allow the pod to be restarted.
- Specify 'busybox' as the image.

##### Steps

1. `kubectl run -it --rm --image=busybox busybox --restart=Never -- echo 'hello world'`
