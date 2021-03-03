# Batch processing

### 1. Create a Job that

- Is named 'hello'
- Uses the image 'busybox'
- Runs the command 'echo hello; sleep 45; echo $HOSTNAME'

Find the name of the Job Pod and follow its logs with:

- `kubectl logs -f $JOB_POD_NAME`

Check the Job status with:

- `kubectl describe job hello`

When it is completed write the Job logs to a file:

- `kubectl logs $JOB_POD_NAME > job.log`

##### Steps

1. `kubectl create job hello --image=busybox -- /bin/sh -c 'echo hello; sleep 45; echo $HOSTNAME'`
2. `kubectl describe job hello`
3. `kubectl get pods`
4. `kubectl logs -f hello-p7w7b`
5. `kubectl logs hello-p7w7b > job.log`
6. `cat job.log`

---

### 2. Create a job that

- Automatically terminates after 30 seconds

Use the provided `job.yaml` file to create the Job. You will need to add the appropriate line to the specification to make sure that the timeout happens. After the timeout happens you can inspect the job events with: `kubectl describe job bb-to`

You should find that the Job was actively terminated.

##### Steps

1. `vim job.yaml`
2. ```yaml
   apiVersion: batch/v1
   kind: Job
   metadata:
     name: bb-to
   spec:
     activeDeadlineSeconds: 30
     template:
       metadata:
         labels:
           run: bb-to
       spec:
         containers:
         - args:
           - /bin/sh
           - -c
           - while true; do echo hello; sleep 10; done
           image: busybox
           name: busybox
         restartPolicy: OnFailure
   ```
3. `kubectl create -f job.yaml`
4. `kubectl describe job bb-to`

---

### 3. Create a job that

- Runs 5 times sequentially

Use the provided `job.yaml` file to create the Job. You will need to add the appropriate line in the specification to make sure that it runs 5 times sequentially.

##### Steps

1. `vim job.yaml`
2. ```yaml
   apiVersion: batch/v1
   kind: Job
   metadata:
     name: bb-5x
   spec:
     completions: 5
     template:
       metadata:
         labels:
           run: bb-5x
       spec:
         containers:
         - args:
           - /bin/sh
           - -c
           - while true; do echo hello; sleep 10; done
           image: busybox
           name: busybox
         restartPolicy: OnFailure
   ```
3. `kubectl create -f job.yaml`
4. `kubectl describe job bb-5x`
5. `kubectl logs job/bb-5x`

---

### 4. Create a job that

- Runs 5 times in parallel

Use the provided `job.yaml` file to create the Job. You will need to add the appropriate lines in the specification to make sure that it runs 5 times in parallel.

##### Steps

1. `vim job.yaml`
2. ```yaml
   apiVersion: batch/v1
   kind: Job
   metadata:
     name: bb-5xp
   spec:
     parallelism: 5
     template:
       metadata:
         labels:
           run: bb-5xp
       spec:
         containers:
         - args:
           - /bin/sh
           - -c
           - while true; do echo hello; sleep 10; done
           image: busybox
           name: busybox
         restartPolicy: OnFailure
   ```
3. `kubectl create -f job.yaml`
4. `kubectl describe job bb-5xp`
5. `kubectl logs job/bb-5xp`

---

### 5. Create a CronJob that

- Is named 'cj'
- Uses the 'busybox' image
- Runs on a `*/1 * * * *` schedule
- Runs `/bin/sh -c 'date; echo Hello'` as command and arguments

After creating the CronJob, inspect the Pods with:

- `kubectl get pods --show-labels`

In the labels, you can find the parent resource for the Pod. Inspect the logs for the CronJob pods with:

- `kubectl logs $CRONJOB_POD`

You should see a different date every time.

##### Steps

1. `kubectl create cronjob cj --image=busybox --schedule='*/1 * * * *' -- /bin/sh -c 'date; echo Hello'`
2. `kubectl describe cronjob cj`
3. `kubectl get pods --show-labels`
4. `kubectl logs cj-1611155880-8pjdc`
5. `kubectl logs cj-1611155940-9lzkv`
