# Core Concepts and Commands

### 1. Use `kubectl` to create a Namespace called `mynamespace`

##### Steps

1. `kubectl create namespace mynamespace`

---

### 2. Use `kubectl` to create a Pod.

Make sure that the Pod:

- Is named 'nginx'
- Uses the 'nginx' image
- Runs in the 'mynamespace' namespace

##### Steps

1. `kubectl run --image=nginx nginx --namespace=mynamespace`

---

### 3. Without actually creating the Namespace, generate the YAML definition for a Namespace called `myns`. Store the Namespace definition in a file called `namespace.yaml`

##### Steps

1. `kubectl get namespace mynamespace -o yaml > namespace.yaml`
2. `vim myns.yaml`
3. ```yaml
   apiVersion: v1
   kind: Namespace
   metadata:
    name: myns
   ```
