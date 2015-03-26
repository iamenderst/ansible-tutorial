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
 $ sudo apt-get install ansible
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

* Install ansible via pip:

##### MAC OS X

```
 $ sudo CFLAGS=-Qunused-arguments CPPFLAGS=-Qunused-arguments pip install ansible
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
 $ make rpm
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
* Most common (and the default mode)
* No need to setup agents or things on nodes
* This is what we are going to use

##### Pull
* Less common
* Useful if you want nodes to check every N minutes on a particular scheduler
* Works by checking configuration orders out of Git on a crontab and the managing the machine locally
* ansible-pull(1)

### SSH access

### Access to Ansible (git) repo

### Privileges for certain commands (SUDO)

### Terminology

##### Management Workstation

* This is the machine where Ansible actually runs
* Ansible can be run from any machine with Python 2.6 installed
* Windows isn’t supported for the control machine :(
* Can be Red Hat, Debian, CentOS, OS X, any of the BSDs, and so on :)
* This is the only place where you need to install Ansible

##### Managed Nodes

* The machines where things happen!
* This are the "Hosts" defined on the Inventory file
* Accessed via SSH (if remote, local actions can also happen)
* The only requirement is Python 2.4 or later
* If running less than Python 2.5, you will also need the package **python-simplejson**

##### [Inventory] (http://docs.ansible.com/intro_inventory.html)

* The file where you describes Hosts and Groups
* Basically, here is where you put the ip/hostnames of the machines you want to manage
* Uses a simple INI format

```
monitor.percona.com

[databases]
db-east.percona.com
db-west.percona.com

[webservers]
static.percona.com
foo.percona.com
```

##### [Modules] (http://docs.ansible.com/modules.html)

* The units of work that Ansible ships out to remote machines
* Little scripts that can be made on any language including Perl, Bash, or Ruby – but can leverage some useful communal library code if written in Python
* Return JSON or simple key=value pairs (though if you are using the command line or playbooks, you don’t really need to know much about that)
* Modules are **idempotent**
* Once modules are executed on remote machines, they are removed
* Documentation can be accessed from the command line:

```
ansible-doc yum
```

##### [Roles] (http://docs.ansible.com/playbooks_roles.html#roles)

* The best way to organize your playbooks
* Roles are ways of automatically loading certain vars_files, tasks, and handlers based on a known file structure
* Roles are just automation around ‘include’ directives, and really don’t contain much additional magic.
* A role may include applying certain variable values, certain tasks, and certain handlers – or just one or more of these things.
* Grouping content by roles also allows easy sharing of roles with other users

Example:
```
site.yml
roles/
   databases/
     files/
     templates/
     tasks/
     handlers/
     vars/
     defaults/
     meta/
   webservers/
     files/
     templates/
     tasks/
     handlers/
     vars/
     defaults/
     meta/

```

##### [Tasks] (http://docs.ansible.com/playbooks_intro.html#tasks-list)

* The goal of each task is to execute a module, with very specific arguments
* Tasks combine an action (a module and its arguments) with a name and optionally some other keywords (like looping directives)
* Tasks are executed in order, one at a time, against all machines matched by the host pattern, before moving on to the next task

Example of a task:
```
tasks:
  - name: make sure apache is running
    service: name=httpd state=running
```

##### [Playbooks] (http://docs.ansible.com/playbooks.html)

* Playbooks exist to run tasks
* A playbook is a list of plays
* A play is minimally a mapping between a set of hosts and the tasks which run on those hosts
* There can be one or many plays in a playbook

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

##### ping

##### setup

##### command



