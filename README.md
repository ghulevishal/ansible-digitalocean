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
