# Setting up your Project

## Setting up your bare git repo



* Create empty Git repository for your project under *ansible_tutorial*

```
 $ cd ansible_tutorial
 $ git init .
```

:information_source: *ansible tutorial* is the directory which contains your *Vagrantfile* which at this point should like as follows:

```
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
    config.vm.box = "perconajayj/centos-x86_64"

    # Master
    config.vm.define "master" do |master|
        master.vm.network "private_network", ip: "192.168.10.100"
        master.vm.hostname = "master"
    end

    # Slave
    config.vm.define "slave" do |slave|
        slave.vm.network "private_network", ip: "192.168.10.101"
        slave.vm.hostname = "slave"
    end
end
 
```


## Creating your inventory file

* Create inventory file with the following layout

```
 $ cat > hosts

[master]
192.168.10.100

[slave]
192.168.10.101
 
[all:children]
master
slave
```

## Bring up your VMs

* Start your VMs with Vagrant

```
 $ vagrant up
```

* Validate that your VMs are running

```
 $ vagrant status

Current machine states:

master                    running (virtualbox)
slave                     running (virtualbox)

This environment represents multiple VMs. The VMs are all listed
above with their current state. For more information about a specific
VM, run `vagrant status NAME`.

```

* Validate Ansible has acess to your VMs

```
 $ ansible -u root --private-key=~/.vagrant.d/insecure_private_key -i hosts -m ping all

The authenticity of host '192.168.10.100 (192.168.10.100)' can't be established.
RSA key fingerprint is b2:a2:16:6a:3c:ca:59:d8:d1:e4:55:67:ad:b8:95:da.
Are you sure you want to continue connecting (yes/no)? yes
The authenticity of host '192.168.10.101 (192.168.10.101)' can't be established.
RSA key fingerprint is b2:a2:16:6a:3c:ca:59:d8:d1:e4:55:67:ad:b8:95:da.
Are you sure you want to continue connecting (yes/no)? yes
192.168.10.100 | success >> {
    "changed": false,
    "ping": "pong"
}

192.168.10.101 | success >> {
    "changed": false,
    "ping": "pong"
}
```

## Setting up defaults

* Create *ansible.cfg* in your *ansible_tutorial* project directory with the following contents:

```
 $ cat ansible.cfg
 
[defaults]
remote_user = root
private_key_file = ~/.vagrant.d/insecure_private_key
hostfile = hosts
```

* Ping your hosts again

```
 $ ansible -m ping all

192.168.10.100 | success >> {
    "changed": false,
    "ping": "pong"
}

192.168.10.101 | success >> {
    "changed": false,
    "ping": "pong"
}
```

## Creating your first simple playbook

* Let's create a basic play to install a few tools and setup the OS

```
 $ mkdir plays
 $ vi plays/setup_os.yml
 $ cat plays/setup_os.yml
---
- name: Set up OS
  hosts: all
  tasks:
    - name: Install EPEL
      yum: name=epel-release state=present

    - name: Install Handy Tools
      yum: name={{ item }} state=present
      with_items:
        - nc
        - socat
        - lsof
        - wget
        - curl
        - screen
        - tmux
        - sysstat
        - ntp
        - ntpdate
        - telnet
        - unzip
        - bind-utils
        - vim
        - perf
        - libselinux-python

    - name: Manage Services
      service: name={{ item.name }} state={{ item.state }}
      with_items:
        - name: ntpd
          state: running
        - name: firewalld
          state: stopped

    - name: Ensure SELINUX is permissive
      selinux: state=permissive policy=targeted

``` 

* Run the play to prepare your VMs

```
 $ ansible-playbook plays/setup_os.yml

PLAY [Set up OS] **************************************************************

GATHERING FACTS ***************************************************************
ok: [192.168.10.100]
ok: [192.168.10.101]

TASK: [Install EPEL] **********************************************************
ok: [192.168.10.101]
ok: [192.168.10.100]

TASK: [Install Handy Tools] ***************************************************
changed: [192.168.10.100] => (item=nc,socat,lsof,wget,curl,screen,tmux,sysstat,ntp,ntpdate,telnet,unzip,bind-utils,vim,perf,libselinux-python)
changed: [192.168.10.101] => (item=nc,socat,lsof,wget,curl,screen,tmux,sysstat,ntp,ntpdate,telnet,unzip,bind-utils,vim,perf,libselinux-python)

TASK: [Manage Services] *******************************************************
changed: [192.168.10.101] => (item={'state': 'running', 'name': 'ntpd'})
changed: [192.168.10.101] => (item={'state': 'stopped', 'name': 'firewalld'})
changed: [192.168.10.100] => (item={'state': 'running', 'name': 'ntpd'})
changed: [192.168.10.100] => (item={'state': 'stopped', 'name': 'firewalld'})

TASK: [Ensure SELINUX is permissive] ******************************************
ok: [192.168.10.101]
ok: [192.168.10.100]

PLAY RECAP ********************************************************************
192.168.10.100             : ok=5    changed=2    unreachable=0    failed=0
192.168.10.101             : ok=5    changed=2    unreachable=0    failed=0

```

## Install MySQL with another play

