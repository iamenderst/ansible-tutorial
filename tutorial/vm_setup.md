[Vagrant downloads page]: http://www.vagrantup.com/downloads
[Vagrant source]: https://github.com/mitchellh/vagrant
[Vagrant MAC installer]: https://dl.bintray.com/mitchellh/vagrant/vagrant_1.7.2.dmg
[Vagrant DEB installer]: https://dl.bintray.com/mitchellh/vagrant/vagrant_1.7.2_x86_64.deb
[Vagrant RPM installer]: https://dl.bintray.com/mitchellh/vagrant/vagrant_1.7.2_x86_64.rpm
[homebrew]: http://brew.sh/
[cask]: http://caskroom.io/


# Setting up VMs


We are going to use Vagrant with VirtualBox in this tutorial. Vagrant is capable of using different providers such as AWS, VMware, etc.  

In the following we are going to perform these steps on a MAC but below we provide instructions for the steps required for Linux.  

Note that all the installer links point to 64 bit installers. If you have a 32 bit machine please grab the corresponding installer from the corresponding download page.

## Installing Vagrant 

Below are the instructions for MAC and Linux. If you have a different platform (Windows) or you have a 32 bit machine you can grab the installer from the [Vagrant downloads page]. If you have another platform, you might need to install it using [Vagrant source].

### Mac OS X


##### Using DMG

* Grab the [Vagrant MAC installer] and click on it to mount the dmg
* Perform install using embedded Vagrant.pkg

##### Using HomeBrew and Cask

* Ensure you have [homebrew] and [cask] installed  
* Install Vagrant with the following command:   
  `# brew cask install vagrant`  

### Debian derivatives

##### Using deb
* Grab the [Vagrant DEB installer]
* Install the package with the following command:  
  `# dpkg -i vagrant_*.deb`

##### Using apt-get
* Check if your distribution includes Vagrant:  
  `# apt-cache search vagrant`
  
* install the package with the following command:  
  `# apt-get install vagrant`

  (note that above assumes package is called vagrant)


### Red Hat derivatives

### Using rpm
* Grab the [Vagrant RPM installer]
* Install the package with the following command:  
  `# rpm -ivh vagrant_*.rpm`

### Using yum

* Check if your repos provide vagrant:  
  `# yum search vagrant`
  
* If they do, install it as follows:  
  `# yum install vagrant`
  
  (note that above assumes package is called vagrant)

### Validate

* Validate that your installation works:  
  `# vagrant --version`


## Installing VirtualBox



## Setting up your first Vagrantfile


* Our goal: setup two minimal install Linux VMs running CentOS 7 that coexist
  on the same network.

