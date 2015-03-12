# -*- mode: ruby -*- 
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
    config.vm.box = "perconajayj/centos-86_64"

    # Master
    config.vm.define "master" do |master|
        master.vm.network "private_network", ip: "192.168.10.100"
        master.vm.hostname = "master"
        master.vm.provision "ansible" do |ansible|
            ansible.playbook = "provisioning/playbooks/mysql_master.yml"
            ansible.limit = 'master'
        end
    end

    # Slave
    config.vm.define "slave" do |slave|
        slave.vm.network "private_network", ip: "192.168.10.101"
        slave.vm.hostname = "slave"
        slave.vm.provision "ansible" do |ansible|
            ansible.playbook = "provisioning/playbooks/mysql_slave.yml"
            ansible.limit = 'slave'
        end
    end
end
