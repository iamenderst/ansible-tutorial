# Setting up your Project

## Setting up your bare git repo

* mkdir ansible-mysql 
* cd ansible-mysql
* git init .

## Creating your inventory file

* Create ${PROJECT}/hosts
* Create groups in hosts [master], [slave], [all:children], ...

## Setting up ansible.cfg to default to your inventory

* vi ansible.cfg 
* edit: inventory=${PROJECT}/hosts
* confirm that you can run ansible and ansible-play without -i

## Creating your first simple playbook

## Introducing code reuse via roles

