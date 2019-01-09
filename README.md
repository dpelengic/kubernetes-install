# Kubernetes installation script

## About

Ansible script which contains three roles:
- docker
- kubernetes
- helm

These roles will install Kubernetes version 1.11.X with RBAC on a Ubuntu/CentOS virtual machine, or bare-metal server. It will also install the following:
- Docker CE
- Kubernetes CNI
- Flannel network plugin (version 0.10.0)
- Helm packet manager

There is also some basic configuration, to make this node ready for pod deployment (even as a standalone node).

## Tested Ansible version

```bash
ansible --version
  ansible 2.5.1
```

## Supported OS

The following OS are supported:
- Ubuntu 18.04 (bionic)
- CentOS 7

The script will automatically include OS specific task plays, according to the Linux distribution running on target server. 

## Before running

Example hosts file contain three servers: centos1, ubuntu1 and ubuntu2

```
[kubernetes-centos]
centos1 ansible_host=10.10.11.10 ansible_user=centos

[kubernetes-ubuntu]
ubuntu1 ansible_host=10.10.11.20 ansible_user=ubuntu
ubuntu1 ansible_host=10.10.11.21 ansible_user=ubuntu
```

The script assumes a passwordless SSH access to the server (use SSH keys).
Make sure you update the hosts file with correct username and server ip/domain.

## Running this script

The following command will run all ansible roles (docker, kubernetes, helm):

```bash
ansible-playbook -i hosts -vv kubernetes.yml 
```

## Available ansible tags

You can run specific parts of this script with the following tags:
- docker - installs docker
- k8s-install - installs kubernetes
- k8s-configure - configures kubernetes using kubeadm
- k8s-reset - runs kubeadm reset, which tears down the cluster (does not uninstall packages though)
- helm - installs helm and initalizes helm's tiller pod

Example using ansible tags, to run only Kubernetes configuration:

```bash
ansible-playbook -i hosts -vv kubernetes.yml --tags k8s-configure
```

## Verify installation

Inspect the kubeinit output, which was generated during installation:

```bash
cat /opt/kubeinit.log
```

Checking out all pods are running on newly provisioned server:

```bash
kubectl get pods --all-namespaces

NAMESPACE     NAME                             READY     STATUS    RESTARTS   AGE
kube-system   coredns-78fcdf6894-d5f42         1/1       Running   0          21h
kube-system   coredns-78fcdf6894-vfkrt         1/1       Running   0          21h
kube-system   etcd-centos                      1/1       Running   0          21h
kube-system   kube-apiserver-centos            1/1       Running   0          21h
kube-system   kube-controller-manager-centos   1/1       Running   0          21h
kube-system   kube-flannel-ds-sftdx            1/1       Running   0          21h
kube-system   kube-proxy-hzjpg                 1/1       Running   0          21h
kube-system   kube-scheduler-centos            1/1       Running   0          21h
kube-system   tiller-deploy-57f988f854-5dz6p   1/1       Running   0          21h
```
