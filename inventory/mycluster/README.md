# How to use in our environment?

### Setup ansible controller env

```
git clone https://github.com/drkjam/netaddr.git
cd netaddr
python setup.py install
python3 setup.py install
pip install --upgrade Jinja2
pip3 install --upgrade Jinja2
```

### Clone this repo

```
git clone https://github.com/sayya9/kubespray.git
cd kubespray
```

### inventory/mycluster/hosts.ini

```
[all]
tp-lab01 ansible_host=192.168.2.21 ip=192.168.2.21

[kube-master]
tp-lab01 	 

[kube-node]
tp-lab01 	 

[etcd]
tp-lab01 	 

[k8s-cluster:children]
kube-node 	 
kube-master 
```

### inventory/mycluster/group_vars/all.yml

Allow kubelet to load kernel modules.

```
kubelet_load_modules: true
```

Use overlay2 as docker storage driver.

```
docker_storage_options: -s overlay2
```

### inventory/mycluster/group_vars/k8s-cluster.yml

Change this to use another Kubernetes version

```
kube_version: v1.9.5
```

Disable insecure port

```
kube_apiserver_insecure_port: 0
```

**External install**

You must run external install for customer server. The purpose of the installation is to download k8s images at first time.

```
ansible-playbook -i inventory/mycluster/hosts.ini -b cluster.yml
```

**Local/Internal install**

copy kubespray to remote customer server

```
scp -r . remote_server:~/kubespray
```

Change your hostname

```
ssh remote_server
vim ~/kubespray/inventory/mylocal/hosts.ini
```

```
tp-lab02 ansible_connection=local local_release_dir={{ansible_env.HOME}}/releases

[kube-master]
tp-lab02

[etcd]
tp-lab02

[kube-node]
tp-lab02

[k8s-cluster:children]
kube-node
kube-master
```

Reset Kubernetes

```
cd kubespray
ansible-playbook -i inventory/mylocal/hosts.ini -b reset.yml
```

Re-install Kubernetes

```
ansible-playbook -i inventory/mylocal/hosts.ini -e kube_version=v1.10.1 -b --skip-tags=download,bootstrap-os cluster.yml
```

**Scale**

```
ansible-playbook -i inventory/mycluster/hosts.ini -u andrew -b scale.yml
```

**Upgrade**

```
ansible-playbook -i inventory/mycluster/hosts.ini -u andrew -b -e kube_version=v1.9.6 --skip-tags=download cluster.yml
```

**Graceful upgrade**

```
ansible-playbook -i inventory/mycluster/hosts.ini -u andrew -b -e kube_version=v1.9.6 --skip-tags=download upgrade-cluster.yml
```

**Reset cluster**

```
ansible-playbook -i inventory/mycluster/hosts.ini -u andrew -b reset.yml
```
