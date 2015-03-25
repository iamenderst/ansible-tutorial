# Setting up your Project

## Setting up your bare git repo



* Create empty Git repository for your project under *ansible_tutorial*

```
 $ cd ansible_tutorial
 $ git init .
```

* *ansible tutorial* is the directory which contains your *Vagrantfile* which at this point should like as follows:

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
ok: [192.168.10.101]
ok: [192.168.10.100]

TASK: [Install EPEL] **********************************************************
ok: [192.168.10.101]
ok: [192.168.10.100]

TASK: [Install Handy Tools] ***************************************************
changed: [192.168.10.100] => (item=nc,socat,lsof,wget,curl,screen,tmux,sysstat,ntp,ntpdate,telnet,unzip,bind-utils,vim,perf,libselinux-python)
changed: [192.168.10.101] => (item=nc,socat,lsof,wget,curl,screen,tmux,sysstat,ntp,ntpdate,telnet,unzip,bind-utils,vim,perf,libselinux-python)

TASK: [Manage Services] *******************************************************
changed: [192.168.10.101] => (item={'state': 'running', 'name': 'ntpd'})
changed: [192.168.10.100] => (item={'state': 'running', 'name': 'ntpd'})
changed: [192.168.10.101] => (item={'state': 'stopped', 'name': 'firewalld'})
changed: [192.168.10.100] => (item={'state': 'stopped', 'name': 'firewalld'})

TASK: [Ensure SELINUX is permissive] ******************************************
ok: [192.168.10.100]
ok: [192.168.10.101]

PLAY RECAP ********************************************************************
192.168.10.100             : ok=5    changed=2    unreachable=0    failed=0
192.168.10.101             : ok=5    changed=2    unreachable=0    failed=0

```

## Install MySQL with another play

* Create another play and populate it with below content:

```
 $ cat plays/setup_mysql.yml
---
- name: Setup MySQL
  hosts: all
  tasks:
    - include_vars: ../vars/percona_repo.yml

    - name: Install MySQL repository
      yum: name={{ percona_yum_repo }} state=present

    - name: Install Percona Server
      yum: name="Percona-Server-server-56" state=present
      register: percona_server

    - name: Install Percona Utilities
      yum: name={{ item }} state=present
      with_items:
        - percona-toolkit
        - percona-xtrabackup

    - name: Generate Random Server ID
      when: percona_server|changed
      shell: echo $RANDOM
      register: random_number

    - name: Capture random number as server_id
      when: random_number|changed
      set_fact: server_id={{ random_number.stdout }}

    - name: Ensure server_id looks sane
      when: random_number|changed
      assert:
        that:
          - server_id is defined
          - server_id|int >= 0
          - server_id|int < 4294967295

    - include_vars: ../vars/mysql_settings.yml

    - name: Configure MySQL
      when: random_number|changed
      template:
        src: ../templates/my.cnf.j2
        dest: /etc/my.cnf
        owner: root
        group: root
      notify:
        - Restart MySQL

  handlers:
    - name: Restart MySQL
      service: name=mysql state=restarted
```

* As you may have spotted from the above, our play now depends on a few external files. One such file is percona_repo.yml and mysql_settings.yml which encapsulate variables for this play. Let's create them:

```
 $ mkdir vars
 $ vi vars/percona_repo.yml
 $ cat vars/percona_repo.yml

---
percona_yum_repo: http://www.percona.com/downloads/percona-release/redhat/0.1-3/percona-release-0.1-3.noarch.rpm

 $ vi vars/mysql_settings.yml
 $ cat vars/mysql_settings.yml
 
---
mysqld:
  server_id: "{{ server_id|int }}"
  datadir: /var/lib/mysql
  log_error: error.log
  innodb_buffer_pool_size: 256M
  innodb_log_file_size: 64M
  innodb_flush_method: O_DIRECT
  innodb_flush_log_at_trx_commit: 2
  query_cache_size: 0
  query_cache_type: 0
  performance_schema: OFF
  log_bin:
  innodb_file_per_table:
  pid-file: /var/run/mysql/mysqld.pid
```

Furthermore, a template called *my.cnf.j2* is referenced. Let's populate it:

```
 $ mkdir templates
 $ vi templates/my.cnf.j2
 $ cat templates/my.cnf.j2

[mysqld]
{% for key, value in mysqld.iteritems() %}
{% if value == None %}
{{ key }}
{% else %}
{{ key }} = {{ value }}
{% endif %}
{% endfor %}

[mysql]
prompt = "{{ ansible_hostname }} mysql> "

[client]
user = root
```

* Now that all the files are present, let's run the play:

```
 $ ansible-playbook plays/setup_mysql.yml

PLAY [Setup MySQL] ************************************************************

GATHERING FACTS ***************************************************************
ok: [192.168.10.100]
ok: [192.168.10.101]

TASK: [include_vars ../vars/percona_repo.yml] *********************************
ok: [192.168.10.101]
ok: [192.168.10.100]

TASK: [Install MySQL repository] **********************************************
changed: [192.168.10.101]
changed: [192.168.10.100]

TASK: [Install Percona Server] ************************************************
changed: [192.168.10.101]
changed: [192.168.10.100]

TASK: [Install Percona Utilities] *********************************************
changed: [192.168.10.101] => (item=percona-toolkit,percona-xtrabackup)
changed: [192.168.10.100] => (item=percona-toolkit,percona-xtrabackup)

TASK: [Generate Random Server ID] *********************************************
changed: [192.168.10.100]
changed: [192.168.10.101]

TASK: [Capture random number as server_id] ************************************
ok: [192.168.10.101]
ok: [192.168.10.100]

TASK: [Ensure server_id looks sane] *******************************************
ok: [192.168.10.100]
ok: [192.168.10.101]

TASK: [include_vars ../vars/mysql_settings.yml] *******************************
ok: [192.168.10.100]
ok: [192.168.10.101]

TASK: [Configure MySQL] *******************************************************
changed: [192.168.10.100]
changed: [192.168.10.101]

NOTIFIED: [Restart MySQL] *****************************************************
changed: [192.168.10.101]
changed: [192.168.10.100]

PLAY RECAP ********************************************************************
192.168.10.100             : ok=11   changed=6    unreachable=0    failed=0
192.168.10.101             : ok=11   changed=6    unreachable=0    failed=0
```