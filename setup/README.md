# Install k3s & Tekton

This is primarily working for the LinuxAcademy/A Cloud Guru Playground.
Build server with Ubuntu base. 2 vCPU/4GB RAM is sufficient.
Then log in as cloud_user

## Install k3s
```
sudo sed -i 's/ALL$/NOPASSWD:ALL/' /etc/sudoers.d/90-cloud-init-users

curl -sfL https://get.k3s.io | sudo sh -
```
Wait about 3min. CPU usage will drop. Make kubectl be able to read the k3s.yaml file.
Alternatively copy it to another machine and run kubectl commands from there.
To keep it simple, I run this locally.

```
sudo chmod a+r /etc/rancher/k3s/k3s.yaml
```
Bash completion. You want it:
```
mkdir ~/.bash_completion.d
kubectl completion bash > ~/.bash_completion.d/kubectl
. ~/.bash_completion.d/kubectl
```

## Install Kubernetes Dashboard

Mainly follow https://rancher.com/docs/k3s/latest/en/installation/

```
GITHUB_URL=https://github.com/kubernetes/dashboard/releases
VERSION_KUBE_DASHBOARD=$(curl -w '%{url_effective}' -I -L -s -S ${GITHUB_URL}/latest -o /dev/null | sed -e 's|.*/||')
kubectl create -f https://raw.githubusercontent.com/kubernetes/dashboard/${VERSION_KUBE_DASHBOARD}/aio/deploy/recommended.yaml

kubectl create -f dashboard.admin-user.yaml -f dashboard.admin-user-role.yaml

#kubectl expose deployment kubernetes-dashboard --type=LoadBalancer --name=dash --namespace=kubernetes-dashboard
kubectl apply -f kubernetes-dashboard-expose.yaml

# Get token
kubectl -n kubernetes-dashboard describe secret admin-user-token | grep '^token'
```
Now connect to https://EXTERNAL-IP:8443 (expect a warning
about self-signed certificate) and use the token to access the K8S dashboard.


## Install Tekton

Watch out for the current releases. I use a working set as of today (2021-05-16):
```
kubectl apply -f https://storage.googleapis.com/tekton-releases/pipeline/previous/v0.24.1/release.yaml
```
Check that the controller and webhook are running: 
```
kubectl get pods -n tekton-pipelines
```
You should see 2 pods: webhook and controller.

## Install Tekton Dashboard

Install the dashboard. Again, check versions:

```
kubectl apply --filename https://storage.googleapis.com/tekton-releases/dashboard/previous/v0.16.1/tekton-dashboard-release.yaml
```
Check that the dashboard is running: 
```
kubectl get pods -n tekton-pipelines
```
You should see an additional dashboard pod.
View the dashboard service:
```
kubectl get svc tekton-dashboard -n tekton-pipelines
```
Should show port 9097 be used.
```
# kubectl port-forward -n tekton-pipelines --address=0.0.0.0 service/tekton-dashboard 9097:9097 > /dev/null 2>&1 &
# kubectl expose deployment --namespace=tekton-pipelines tekton-dashboard --type=LoadBalancer --name=tektondash
# Change to a not-firewalled-port 8097
# kubectl expose deployment --namespace=tekton-pipelines tekton-dashboard --type=LoadBalancer --name=tektondash --port=8097 --target-port=9097
kubectl apply -f tekton-dashboard-expose.yaml
```
Verify with
```
kubectl get service --namespace=tekton-pipelines
```
and connect to http://EXTERNAL-IP:8097/ to get the Tekton dashboard

