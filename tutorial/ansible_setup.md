[homebrew]: http://brew.sh/

[Ansible install page]: http://docs.ansible.com/intro_installation.html
[Ansible source]: https://github.com/ansible/ansible

[Installing Ansible From Source]: ansible_setup.md#installing-ansible-from-source
[Installing Ansible Using Pip]: ansible_setup.md#installing-ansible-using-pip

[Ansible Configuration Options]: http://docs.ansible.com/intro_configuration.html

# Setting up Ansible


## Installing Ansible

### Requirements

* Python
  * Already installed most of the time (LSB)
  * Management Workstation (>2.6)
  * Managed Hosts (>2.4)
* Additional Python modules
  * python-simplejson (python 2.4)
  * libselinux-python (for SELinux management)
* OpenSSH vs. Paramiko

More information and detailed instructions can be found on [Ansible install page]. Below are the steps to perform the install on MAC and Linux. The version of Ansible that we are going to use is 1.8.

### Mac OS X

##### Using Pip

* See [Installing Ansible Using Pip]
 
##### Using HomeBrew and Cask

* Ensure you have [homebrew] installed  
* Install Ansible with the following command:   

```
 $ brew install ansible
``` 

### Debian derivatives

##### Using DEB

* See [Installing Ansible From Source]

##### Using apt-get

* Check if your distribution includes Ansible:  

```
 $ apt-cache search ansible
```

* Some distributions may ship an older version (1.5, for instance) of Ansible. To install a more recent version, set up the following PPA and refresh the cache:  

```
 $ sudo apt-get install software-properties-common
 $ sudo apt-add-repository ppa:ansible/ansible
 $ sudo apt-get update
``` 
  
* Install the package with the following command:  

```
 $ apt-get install ansible
```

  (note that above assumes package is called ansible)


### Red Hat derivatives

##### Using RPM

* See [Installing Ansible From Source]

##### Using yum

* Check if your repos provide ansible:  

``` 
 $ yum search ansible
```
  
* If they do, install it as follows:  

```
 $ sudo yum install ansible
```
  
  (note that above assumes package is called ansible)


### Installing Ansible Using Pip

* Install Pip if not installed yet

```
 $ sudo easy_install pip
```

* Install ansible via pip

##### MAC OS X

```
 $ CFLAGS=-Qunused-arguments CPPFLAGS=-Qunused-arguments pip install ansible
```

##### Linux

```
 $ sudo pip install ansible
```

### Installing Ansible From Source

* Download the source code and its dependencies and setup your environment with the following commands:  

```
 $ git clone git://github.com/ansible/ansible.git --recursive
 $ cd ./ansible
 $ source ./hacking/env-setup
```
* Install pip if it is not installed:  

```
 $ sudo easy_install pip
```
* Additionally, you might have to install some python dependencies:  

```
 $ sudo pip install paramiko PyYAML Jinja2 httplib2
```
* Create a package for your OS and distribution and install it:
 
##### Debian derivatives  
``` 
 $ make deb
 $ sudo dpkg -i ansible*.deb
```

##### Red Hat derivatives
```
 $ make deb
 $ sudo rpm -ivh ansible*.rpm
```

### Validate

* Validate that your installation works and is the right version:  

```
 $ ansible --version
```


## Ansible Architecture

### Operation modes

##### Push
* Most common
* This is what we are going to use

###### Pull
* Less common
* ansible-pull(1)

### SSH access

### Access to Ansible (git) repo

### Privileges for certain commands (SUDO)

### Terminology

##### Management Workstation

##### Managed Nodes

##### Inventory

##### Modules

##### Roles

##### Tasks

##### Playbooks


## Configuring Ansible

### Default locations

* Per system: /etc/ansible.cfg
* Per user: ~/ansible.cfg
* Per project: ${PROJECT}/ansible.cfg
* Explaining inventory lookup
  * /etc/ansible/hosts
  * ${PROJECT}/hosts (segway into ansible.cfg!)

### Setting up defaults with ansible.cfg

* [Ansible Configuration Options]
* Setting up ansible.cfg
* Project settings
* SSH settings
* Gotchas (when not running from the directory which contains ansible.cfg)


## Ansible Basics

### Ping

* ansible -m ping master

### Facts

* ansible -m setup master

### Uptime

* ansible -a ...


### Ansible Modules

#### Core modules

###### ping

##### setup

##### command



