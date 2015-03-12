[homebrew]: http://brew.sh/
[cask]: http://caskroom.io/

[Vagrant downloads page]: http://www.vagrantup.com/downloads
[Vagrant source]: https://github.com/mitchellh/vagrant
[Vagrant MAC installer]: https://dl.bintray.com/mitchellh/vagrant/vagrant_1.7.2.dmg
[Vagrant DEB installer]: https://dl.bintray.com/mitchellh/vagrant/vagrant_1.7.2_x86_64.deb
[Vagrant RPM installer]: https://dl.bintray.com/mitchellh/vagrant/vagrant_1.7.2_x86_64.rpm

[VirtualBox downloads page]: https://www.virtualbox.org/wiki/Downloads
[VirtualBox MAC installer]: http://download.virtualbox.org/virtualbox/4.3.24/VirtualBox-4.3.24-98716-OSX.dmg
[VirtualBox Linux Downloads]: https://www.virtualbox.org/wiki/Linux_Downloads


# Setting up VMs


We are going to use Vagrant with VirtualBox in this tutorial. Vagrant is capable of using different providers such as AWS, VMware, etc.  

In the following we are going to perform these steps on a MAC but below we provide instructions for the steps required for Linux.  

Note that all the installer links point to 64 bit installers. If you have a 32 bit machine please grab the corresponding installer from the corresponding download page.

## Installing Vagrant 

Below are the installation instructions for MAC and Linux. The version that we are going to use is 1.7.2. If you have a different platform (Windows) or you have a 32 bit machine you can grab the installer from the [Vagrant downloads page]. If you have another platform, you might need to install it using [Vagrant source].



### Mac OS X


##### Using DMG

* Grab the [Vagrant MAC installer] and click on it to mount the dmg
* Perform install using embedded Vagrant.pkg

##### Using HomeBrew and Cask

* Ensure you have [homebrew] and [cask] installed  
* Install Vagrant with the following command:   

```
 $ brew cask install vagrant
```

### Debian derivatives

##### Using deb
* Grab the [Vagrant DEB installer]
* Install the package with the following command:  

```
 $ dpkg -i vagrant_*.deb
```

##### Using apt-get
* Check if your distribution includes Vagrant:  

```
 $ apt-cache search vagrant
```
  
* Install the package with the following command:  

```
 $ apt-get install vagrant
```

  (note that above assumes package is called vagrant)


### Red Hat derivatives

##### Using rpm
* Grab the [Vagrant RPM installer]
* Install the package with the following command:  

```
 $ sudo rpm -ivh vagrant_*.rpm
```

##### Using yum

* Check if your repos provide vagrant:  

```
 $ yum search vagrant
``` 
  
* If they do, install it as follows:  

```
 $ sudo yum install vagrant
```
  
  (note that above assumes package is called vagrant)

### Validate

* Validate that your installation works:  

```
 $ vagrant --version
```


## Installing VirtualBox

Below are the instructions for MAC and Linux. The version that we are going to use is 4.3. If you have a different platform (Windows) or you have a more custom machine, alternative installers and the source code can be obtained from the [VirtualBox downloads page]. 

### Mac OS X


##### Using DMG

* Grab the [VirtualBox MAC installer] and click on it to mount the dmg
* Perform install using embedded VirtualBox.pkg

##### Using HomeBrew and Cask

* Ensure you have [homebrew] and [cask] installed  
* Install Vagrant with the following command:   

```
 $ brew cask install virtualbox
```  

### Debian derivatives

##### Using deb
* Grab the DEB for your distribution from [VirtualBox Linux Downloads]* 
* Install the package with the following command:  

```
 $ sudo dpkg -i virtualbox_*.deb
``` 

##### Using apt-get
* Check if your distribution includes VirtualBox:  

```
 $ apt-cache search virtualbox
```

* If not, you might have to setup the VirtualBox repo. Instructions can be found on the [VirtualBox Linux Downloads] page
  
* Install the package with the following command:  

```
 $ sudo apt-get install virtualbox
``` 

  (note that above assumes package is called virtualbox)


### Red Hat derivatives

##### Using rpm
* Grab the RPM for your distribution from [VirtualBox Linux Downloads]
* Install the package with the following command:  

```
 $ sudo rpm -ivh VirtualBox_*.rpm
```

##### Using yum

* Check if your repos provide VirtualBox:  

```
 $ yum search VirtualBox
```

* If not, you might have to setup the VirtualBox repo. Instructions can be found on the [VirtualBox Linux Downloads] page
  
* Once you have a repository that provides the package, you can install it as follows:  

```
 $ yum install VirtualBox-4.3
``` 
  
  (note that above assumes package is called virtualbox)

### Validate

* Validate that your installation works:  

```
 $ VBoxManage list vms
``` 
  
  (should return empty at this point)

## Setting up Vagrantfile

Our goal is to setup two Linux VMs that are on the same "private" network, preferably running a minimal installation of CentOS 7.

To achieve that, we are going to create a simple Vagrantfile to describe the setup.

:warning: **Create a project directory and put your Vagrantfile in there!** :warning:

```
 $ mkdir ansible_tutorial
 $ cat > Vagrantfile
 [copy and paste Vagrantfile contents here]
 ^D
````

The contents of the *initial* Vagrantfile (we are going to modify it soon) should look as follows:

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

### Validate

* Make sure you are in your project directory (**ansible_tutorial/**)

* Validate that your Vagrantfile looks consistent with the snippet above     

```
 $ cat Vagrantfile
```
  
* Start up your VMs:  

```
 $ vagrant up
``` 

* Log on to your **master** VM:  

```
 $ vagrant ssh master
```
  
* After login your prompt should look like this:  

```
[vagrant@master ~]$
```