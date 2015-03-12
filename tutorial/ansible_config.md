Ansible Configuration
=====================

Default locations
-----------------
* Per system: /etc/ansible.cfg
* Per user: ~/ansible.cfg 
* Per project: ${PROJECT}/ansible.cfg
* Explaining inventory lookup
  * /etc/ansible/hosts
  * ${PROJECT}/hosts (segway into ansible.cfg!)

Setting up defaults with ansible.cfg
------------------------------------
* Setting up ansible.cfg
  * [Options](http://docs.ansible.com/intro_configuration.html)
* Project settings
* SSH settings
* Gotchas (when not running from the directory which contains ansible.cfg)
