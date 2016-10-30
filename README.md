# Kubernetes on Xen using kubeadm and Ansible

## Install Ansible and Direnv on your local machine.

`brew install ansible direnv`

## Get your hosts.ini filled out.

```
cp hosts.ini.dist hosts.ini
vi hosts.ini
```

## Get the Ubuntu nodes ready for Ansible.

`ansible all -i hosts.ini -u root -m raw -a "apt-get install -y python2.7 python-simplejson"``

## Install Docker and Kubernetes dependencies.

`ansible-playbook -i hosts.ini setup.yml`

## Setup the Master.

`ansible-playbook -i hosts.ini master.yml`

## The debug output contains the token you need.

Update `envrc` with the token and ip address of the master:

```
cp envrc .envrc
vi .envrc
```

## Setup the pod network from the Master.

```
curl -s https://raw.githubusercontent.com/tigera/canal/master/k8s-install/kubeadm/canal.yaml > canal.yaml
kubectl --kubeconfig /etc/kubernetes/admin.conf create -f canal.yaml
```

## Join the nodes to the Master.

`ansible-playbook -i hosts.ini nodes.yml`

## Watch them join from the Master.

`kubectl --kubeconfig /etc/kubernetes/admin.conf get pods --all-namespaces`
