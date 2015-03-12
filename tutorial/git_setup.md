[homebrew]: http://brew.sh/

[Git Downloads page]: http://git-scm.com/downloads
[Git MAC installer]: http://git-scm.com/download/mac
[Git Documentation]: http://git-scm.com/doc


# Setting up Git

Git is frequently the solution of choice for version control for Ansible repositories. Outside of that you also might need Git to install tools from source. Therefore, we will cover its basic setup and installation.

## Installing Git

The version of git we use is 2.3.2.

### Mac OS X

##### Using DMG

* Grab the [Git MAC installer] and click on it to mount the dmg
* Perform install using embedded git pkg  
  (git-2.2.1-intel-universal-mavericks.pkg on my platform)

##### Using HomeBrew and Cask

* Ensure you have [homebrew] installed  
* Install Git with the following command:   

```
 $ brew install git
```

### Debian derivatives

##### Using apt-get

* Most distributions ship git, which you can install as follows:  

```
 $ sudo apt-get install git
```

### Red Hat derivatives

##### Using yum
  
* As most distributions ship git, you should be able to install it with below command:

```
 $ sudo yum install git
```

### Validate

* Validate that your installation works:  

```
 $ git --version
```


## Configuring Git

You can learn more about Git configuration in the [Git Documentation]. The steps required to set up your persona after installation can be seen below.

* Configure your github email and verbose username:

```
 $ git config --global user.name "Robert Barabas"
 $ git config --global user.email robert.barabas@example.com
```

* Alternatively, you can adjust the editor you would like to use with git with the following command:

```
 $ git config --global core.editor vim
```


