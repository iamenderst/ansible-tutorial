Role Name
=========

This role install and sets up a mysql server instance on a machine.

Requirements
------------

Currently the role only support Linux and FreeBSD.

Role Variables
--------------

Required variables:
 - server ID

Tunables:
 - bin-log

Dependencies
------------

As the role automatically installs mysql client it has a dependency on the mysql-client role.

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: mysql_servers
      sudo: yes
      roles:
         - { role: mysql-server, serverid: 12 }

License
-------

BSD

Author Information
------------------

Robert BARABAS (robert.barabas@)
