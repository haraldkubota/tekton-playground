# tekton-playground

This is mainly for using the LinuxAcademy/A Cloud Guru's playground.
Those are servers which you can use for learning. They are hosted on the
Internet and thus you can do things like exposing services to the Internet.
Note that only selected incoming ports are allowed.
See [here](https://support.linuxacademy.com/hc/en-us/articles/360026494432-Open-Firewall-Rules-On-Servers-Cloud-Playground)
From 8000 to 8999 those are open for incoming traffic:
* 8000-8100
* 8140
* 8142
* 8443

## setup/

[Set up k3s and Tekton](https://github.com/haraldkubota/tekton-playground/tree/main/setup)
and expose both the Kubernetes and Tekton dashboards

## ~/.kube/config.yaml

Normally this is needed on the client machines, but since in this case the server is the client too in order to keep this simple,
copy the k3s.yaml file into the standard location in ~/.kube/
```
mkdir ~/.kube
sudo cat /etc/rancher/k3s/k3s.yaml | cat > ~/.kube/config.yaml
echo "export KUBECONFIG=~/.kube/config.yaml" >>~/.bashrc

```

## Install tkn

```
wget https://github.com/tektoncd/cli/releases/download/v0.18.0/tektoncd-cli-0.18.0_Linux-64bit.deb
sudo dpkg -i ./tektoncd-cli-0.18.0_Linux-64bit.deb
```

