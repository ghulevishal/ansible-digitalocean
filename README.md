# ansible-digitalocean

## Install Ansible latest version.
```
$ sudo apt-get update
$ sudo apt-get install software-properties-common
$ sudo apt-add-repository ppa:ansible/ansible
$ sudo apt-get update
$ sudo apt-get install ansible
$ sudo apt-get install python-pip
$ sudo pip install dopy
```

## Create Nodes
- Clone the repo.
```
$ git clone https://github.com/vishalcloudyuga/ansible-digitalocean.git
```

- Cd into the directory 
```
$ cd /ansible-digitalocean/do-cluster
```

- Edit `digitalocean.yml` and update the `$DO_API_TOKEN` with your Digital Ocean API Token.

- Execute the PlayBook.
```
$ ansible-playbook digitalocean.yml 
[DEPRECATION WARNING]: [defaults]hostfile option, The key is misleading as it 
can also be a list of hosts, a directory or a list of paths . This feature will
 be removed in version 2.8. Deprecation warnings can be disabled by setting 
deprecation_warnings=False in ansible.cfg.

PLAY [digitalocean] ************************************************************

TASK [Gathering Facts] *********************************************************
ok: [localhost]

TASK [ensure ssh key exists] ***************************************************
ok: [localhost]

TASK [ensure key exists at DigitalOcean] ***************************************
changed: [localhost]

TASK [Create master] ***********************************************************
changed: [localhost]

TASK [debug] *******************************************************************
ok: [localhost] => {
    "msg": "IP is 128.199.156.83"
}

TASK [Create worker] ***********************************************************
changed: [localhost]

TASK [debug] *******************************************************************
ok: [localhost] => {
    "msg": "IP is 128.199.112.137"
}

PLAY RECAP *********************************************************************
localhost                  : ok=7    changed=3    unreachable=0    failed=0   

```

- Update the `hosts` file with this recently create master and worker ip addresses.
```
$ vim hosts

[digitalocean]
localhost ansible_connection=local
[nodes]
128.199.156.83
128.199.112.137
```

- Install Python on each Node.
```
$ ansible-playbook python.yaml -u root --private-key=~/.ssh/vishal -i hosts 

[DEPRECATION WARNING]: [defaults]hostfile option, The key is misleading as it 
can also be a list of hosts, a directory or a list of paths . This feature will
 be removed in version 2.8. Deprecation warnings can be disabled by setting 
deprecation_warnings=False in ansible.cfg.

PLAY [nodes] *******************************************************************

TASK [install python 2] ********************************************************
changed: [128.199.112.137]
changed: [128.199.156.83]

PLAY RECAP *********************************************************************
128.199.112.137            : ok=1    changed=1    unreachable=0    failed=0   
128.199.156.83             : ok=1    changed=1    unreachable=0    failed=0   
```

## Install Kubernetes cluster.

- Go to the `ansible-digitalocean/k8s-install` directory.

- Update the `inventory` with workers and master's IP address.
```
$ vim inventory

[master]
128.199.156.83

[node]
128.199.112.137

[kube-cluster:children]
master
node
```

- Install the cluster using Ansible Playbook.
```
$ ansible-playbook site.yaml -u root --private-key=~/.ssh/vishal -i inventory 

PLAY [kube-cluster] ************************************************************

TASK [commons/os-checker : Get os_version from /etc/os-release] ****************
ok: [128.199.156.83]
ok: [128.199.112.137]

TASK [commons/os-checker : Get distro name from /etc/os-release] ***************
ok: [128.199.156.83]
ok: [128.199.112.137]

TASK [commons/os-checker : Set fact ansible_os_family var to Debian] ***********
skipping: [128.199.156.83]
skipping: [128.199.112.137]

TASK [commons/os-checker : Set fact ansible_os_family var to Debian] ***********
ok: [128.199.156.83]
ok: [128.199.112.137]

TASK [commons/os-checker : Set fact ansible_os_family var to RedHat] ***********
skipping: [128.199.156.83]
skipping: [128.199.112.137]

TASK [commons/os-checker : Override config file directory for Debian] **********
ok: [128.199.156.83]
ok: [128.199.112.137]

TASK [docker : Install Docker container engine] ********************************
included: /root/ansible-digitalocean/k8s-install/roles/docker/tasks/pkg.yml for 128.199.156.83, 128.199.112.137

TASK [docker : Add Docker APT GPG key] *****************************************
changed: [128.199.156.83]
changed: [128.199.112.137]

TASK [docker : Add Docker APT repository] **************************************
```

- Login to the Master Node and check the nodes.
```
$ kubectl get node
NAME      STATUS    ROLES     AGE       VERSION
m1        Ready     master    5m        v1.9.3
n1        Ready     <none>    3m        v1.9.3 
```

## Delete Cluster and Nodes and SSH key at the Digital Ocean.

- Get into the `ansible-digitalocean/do-cluster/` directory.

-  Edit `delete.yaml` and update the `$DO_API_TOKEN` with your Digital Ocean API Token.

- Execute following Ansible palybook
```
$ ansible-playbook delete.yaml 

[DEPRECATION WARNING]: [defaults]hostfile option, The key is misleading as it 
can also be a list of hosts, a directory or a list of paths . This feature will
 be removed in version 2.8. Deprecation warnings can be disabled by setting 
deprecation_warnings=False in ansible.cfg.

PLAY [digitalocean] ************************************************************

TASK [Gathering Facts] *********************************************************
ok: [localhost]

TASK [ensure ssh key exists] ***************************************************
ok: [localhost]

TASK [ensure key exists at DigitalOcean] ***************************************
ok: [localhost]

TASK [Delete master] ***********************************************************
changed: [localhost]

TASK [Delete worker] ***********************************************************
changed: [localhost]

TASK [ensure key exists at DigitalOcean] ***************************************
changed: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=6    changed=3    unreachable=0    failed=0   

```
