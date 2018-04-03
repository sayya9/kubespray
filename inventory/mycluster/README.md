# How to use in our environment?

### Prepare

**Install python-netaddr**

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

**Install**

```
ansible-playbook -i inventory/mycluster/hosts.ini -u andrew -b cluster.yml
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
