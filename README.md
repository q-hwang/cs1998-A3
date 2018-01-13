# cs1998-A3
DevOps

This project is to simulate the deployment in vagrant VM and deploy A2 to Amazon AWS server
http://52.27.242.248/boards

Original assignment: https://github.com/Cornell-PoBE/A3

## Table of Contents

* [System Configuration](#system-configuration)
* [Functionality](#functionality)
* [Reference](#reference)
* [Issues](#issues)

## System Configuration

Install requirements in `vene-requirements.txt`

## Functionality

### Simulate the deployment in vagrant 

`bento/ubuntu-16.04` is used to simulate the deployment

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
The app should be accessible now in localhost:8080/boards

### Deploy to server

The server used in this project is `ubuntu 16.04 LTS` with public IP 52.27.242.248 and private key named `a3keypair.pem` 

Configure the public IP in `hosts`
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

## Reference

Original assignment: https://github.com/Cornell-PoBE/A3

The frontend files (404.html, index.html, styles.css and bundle.js) are provided by instructors and edited by me latter.

## Issues

1. Upstart

The original assignment was designed for ubuntu 14.04. But the server from AWS I found is ubuntu 16.04, which seems do not support upstart but systemd instead. 

So the upstart script is changed from upstart.conf.j2 to upstart.service. 

In recent tests, the upstart script sometimes fails to start the app at the first time. Manually start the app using gunicorn is needed. Then it works correctly.

2. Migration

During database migration, there would be error if the alembic_version does not meet between the folder `migration` and the table `alembic_version` in mysql. This happens when the migration folder is deleted or the app is reinstalled while the table `alembic_version` is still in mysql. 

The simplest way is to delete migration folder and drop the table `alembic_version`. The data in database is kept in this process. Then run `python manage.py db init` again.

