# CKAD Exercises

A set of exercises that helped me prepare for the Certified Kubernetes Application Developer exam, offered by Xebia and organized by curriculum domain. They may as well serve as learning and practicing with Kubernetes.

During the exam, you are allowed to keep one other browser tab open. It is recommended that you read official documents before attempting the exercises or the exam.

- <https://kubernetes.io/docs/home/>

### CKAD Curriculum

Knowledge, Skills and Abilities that a Certified Kubernetes Application Developer can be expected to demonstrate:

- 13% Core Concepts
- 18% Configuration
- 10% Multi-Container Pods
- 18% Observability
- 20% Pod Design
- 13% Services & Networking
- 8% State Persistence

### CKAD Exam

The online, proctored, performance-based test consists of a set of performance-based items (problems) to be solved in a command line and is expected to take approximately two hours to complete.

##### Exam Tips

- Need to know the kubectl commands and YAML syntax.
- Terminal knowledge is required.
- You have full access to kubernetes.io and `kubectl`, make the most of it.
- Know the layout of the documentation.
- Browse the YAML API.
  `kubectl explain <object>.<subobject> [--recursive]`
- Convert resources to YAML.
  `kubectl get <resource> --export=true -o yaml`
- Ensure you know what namespaces means and how to use it with `kubectl` commands.
- Don’t spend too much time on one question. Do questions with a lower weight (e.g. between 2% and 4%) later.

### Bookmarks

A set of bookmarks is added to this repository. These can be found at **documents** -> **bookmarks.html**
