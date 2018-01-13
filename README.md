# cs1998-A3
DevOps

This project is to simulate the deployment in vagrant VM and deploy A2 to Amazon AWS server
http://52.27.242.248/boards

Original assignment: https://github.com/Cornell-PoBE/A3

## Table of Contents

* [System Configuration](#system-configuration)
* [Functionality](#functionality)
* [Testing](#testing)
* [Reference](#reference)

## System Configuration

Install requirements in [vene-requirements.txt] 

## Functionality

### Simulate the deployment in vagrant 

[bento/ubuntu-16.04] is used to simulate the deployment

```bash
$ git clone https://github.com/q-hwang/cs1998-A3
$ cd A3
$ pwd
<CURR_DIRECTORY>/A3
$ mkdir vagrant
$ cd vagrant
$ vagrant init
$ rm Vagrantfile
$ cd ..
$ cp files/* vagrant/
$ cd vagrant
$ vagrant up
```

### Deploy to server

Configure the public IP in [hosts]
hosts:
```
[webservers]
 <PublicIP> ansible_ssh_user=ubuntu
```

```bash
$ cd vagrant
$ chmod 400 a3keypair.pem 
$ ansible -m ping webservers --private-key=a3keypair.pem --inventory=hosts --user=ubuntu # ping and add host
The authenticity of host '<PublicIP> (<PublicIP>)' cant be established.
ECDSA key fingerprint is SHA256:Y35MHCMwSCH/GZyak+HWkd5bUCKm6JtMOuMCxMAnpkI.
Are you sure you want to continue connecting (yes/no)? yes
<PublicIP> | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
$ ansible-playbook -v site.yml # ansible runs your app on EC2
Using /Users/ilanfilonenko/flaskplate/vagrant/ansible.cfg as config file

PLAY [Deploying your A2] ****************************************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************************************
ok: [<PublicIP>]
...
...
PLAY RECAP ******************************************************************************************************************************************************************************
<PublicIP>                 : ok=16   changed=12   unreachable=0    failed=0
```




