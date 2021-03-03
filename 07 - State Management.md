# State Management

### 1. Create a Pod that

- Is named 'busybox'.
- Has two containers that use image 'busybox' and run with arguments 'sleep' and '3600'.
- Has an 'emptyDir' volume called 'shared' which is mounted in both containers at path '/tmp/shared'.

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
       command: ["/bin/sh", "-c"]
       args: ["sleep 3600"]
       volumeMounts:
       - name: shared
         mountPath: /tmp/shared
     - name: busybox2
       image: busybox
       command: ["/bin/sh", "-c"]
       args: ["sleep 3600"]
       volumeMounts:
       - name: shared
         mountPath: /tmp/shared
     volumes:
       - name: shared
         emptyDir: {}
     dnsPolicy: ClusterFirst
     restartPolicy: Always
   status: {}
   ```
4. `kubectl create -f busybox-pod.yaml`

---

### 2. Write a file to the volume of the second busybox container

- `kubectl exec -it busybox -c bb2 -- /bin/sh -c 'echo "hello" > /tmp/shared/world.txt'`

Inspect the file on the volume in the first busybox container.

- `kubectl exec -it busybox -c bb1 -- cat /tmp/shared/world.txt`

It should output the word 'hello'. The containers are sharing the same volume!

##### Steps

1. `kubectl exec -it busybox -c busybox2 -- /bin/sh -c 'echo "hello" > /tmp/shared/world.txt'`
2. `kubectl exec -it busybox -c busybox1 -- cat /tmp/shared/world.txt`

---

### 3. Create a PersistentVolume that

- Has a size of 10Gi.
- Is named 'mypv'.
- Has an accessMode of 'ReadWriteOnce' and 'ReadWriteMany'.
- Has a storageClassName of 'normal',
- Is mounted with a hostPath of '/etc/foo'.

##### Steps

1. `vim pv.yaml`
2. ```yaml
   apiVersion: v1
   kind: PersistentVolume
   metadata:
     name: mypv
   spec:
     capacity:
       storage: 10Gi
     storageClassName: normal
     accessModes:
       - ReadWriteOnce
       - ReadWriteMany
     hostPath:
       path: "/etc/foo"
   ```
3. `kubectl create -f pv.yaml`
4. `kubectl get pv myvolume`

---

### 4. Create a PersistentVolumeClaim that

- Requests 4Gi of storage.
- Is named 'mypvc'.
- Requests an accessMode of 'ReadWriteOnce'.
- Has a storageClassName of 'normal'.

##### Steps

1. `vim pvc.yaml`
2. ```yaml
   apiVersion: v1
   kind: PersistentVolumeClaim
   metadata:
     name: mypvc
   spec:
     storageClassName: normal
     accessModes:
       - ReadWriteOnce
     resources:
       requests:
         storage: 4Gi
   ```
3. `kubectl create -f pvc.yaml`
4. `kubectl get pvc mypvc`

---

### 5. Create a Pod that

- Is named 'busybox'
- Uses the 'busybox' image
- Passes the arguments 'sleep' and '3600' to the container
- Uses the PersistentVolumeClaim called 'mypvc' as a volume called 'pvcvolume' and mounts it to the path '/etc/foo'

Run the following command to write a file to the volume:

- `kubectl exec -it busybox -- /bin/sh -c 'echo "hello" > /etc/foo/world.txt'`

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
       command: ['/bin/sh', '-c', 'sleep 3600']
       volumeMounts:
       - mountPath: "/etc/foo"
         name: myvolume
     volumes:
     - name: myvolume
       persistentVolumeClaim:
         claimName: mypvc
     dnsPolicy: ClusterFirst
     restartPolicy: Always
   status: {}
   ```
4. `kubectl create -f pod.yaml`
5. `kubectl exec -it busybox -- /bin/sh -c 'echo "hello" > /etc/foo/world.txt'`

---

### 6. Create a Pod that

- Is named 'busybox2'
- Uses the 'busybox' image
- Passes the arguments 'sleep' and '3600' to the container
- Uses the PersistentVolumeClaim called 'mypvc' as a volume called 'pvcvolume' and mounts it to the path '/etc/foo'

Run the following command to ensure that the volume contains the file you created in the previous challenge:

- `kubectl exec -it busybox2 -- cat /etc/foo/world.txt.`

##### Steps

1. `vim busybox-pod.yaml`
2. ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: busybox2
   spec:
     containers:
     - args:
       - "sleep"
       - "3600"
       image: busybox
       name: busybox2
       volumeMounts:
       - name: pvcvolume
         mountPath: "/etc/foo"
     volumes:
     - name: pvcvolume
       persistentVolumeClaim:
         claimName: mypvc
   ```
3. `kubectl create -f pod.yaml`
4. `kubectl exec -it busybox2 -- cat /etc/foo/world.txt.`

---

### 7. Copy '/etc/passwd' from the 'busybox' Pod to your local system

##### Steps

1. `kubectl cp busybox:etc/passwd ./passwd`
2. `cat passwd`
