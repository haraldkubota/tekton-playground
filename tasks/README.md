# Tasks

## Define

```
$ kubectl apply -f task-hello.yaml 
task.tekton.dev/hello configured
```

## Run

```
$ tkn task start hello --dry-run 
apiVersion: tekton.dev/v1beta1
kind: TaskRun
metadata:
  creationTimestamp: null
  generateName: hello-run-
  namespace: default
spec:
  resources: {}
  serviceAccountName: ""
  taskRef:
    name: hello
status:
  podName: ""

$ tkn task start hello 
TaskRun started: hello-run-2hqrp

In order to track the TaskRun progress run:
tkn taskrun logs hello-run-2hqrp -f -n default

$ tkn taskrun logs hello-run-2hqrp -f -n default
[hello] Hello World!
```

