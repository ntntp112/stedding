# Stedding
Stedding is a minimalistic LEMP Stack setup for Laravel *in progress*. It facilitates the setting up of Laravel apps on a well prepared server using Ansible Playbooks. It provisions your VPS with all the tools necessary to run your Laravel PHP application with ease.

## Note
Again, this is still a work in progress. So use it wisely and backup when possible. Better still to test on a barebone server and send in pull requests :)

## Sources
* [Digital Ocean Sudo User Setup](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-14-04)
* [Digital Ocean Basic PHP App Setup](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-basic-php-application-using-ansible-on-ubuntu-14-04)
* [Pogorelov-SS' Github Private Repo Clone Gist](https://gist.github.com/pogorelov-ss/41893e17c7c4776d4d57)

## Local Box Requirements
You need to have Ansible installed on your local computer. This really differs from box to box See [Ansible Documents](http://docs.ansible.com/ansible/intro_installation.html) for instructions. 

## Remote Server Preparations
To run Ansible Playbooks properly on Ubuntu 16.0.4 we need to setup a sudo user and make sure Python and some other packages are available so Ansible can run. The setting up of a sudo user and adding of the SSH keys for it can be done following the instructions below. In the future we will integrate this in a shell script.

### Setup Sudo User laravel
Log into your remote server as root using
````
ssh root@ip
````
Then add a new sudo user:
````
adduser laravel
gpasswd -a laravel sudo
````
#### Public Key Addition to Authorized Keys
Then add your own public key to the authorized keys for your user laravel:
````
su laravel
cd
mkdir .ssh
chmod 700 .ssh
nano .ssh/authorized_keys
`````
add key from local box cat ~/.ssh/id_rsa.pub and exit twice to log off the box
````
chmod 600 .ssh/authorized_keys
exit
exit
````
### Block Root SSH Access
Now on your local box do the following:
````
ssh laravel@ip
````
Then on the remote do:
````
sudo su
nano /etc/ssh/sshd_config
````
Make sure you set root access to no to secure the box
````
PermitRootLogin no
````
Then you need to restart the SSH server:
````
service ssh restart
````
And log off as root and exit box as laravel:
````
exit
exit
````
## Local Ansible Config Setup

We expect you to have installed Ansible on your own control box already. If not check out Ansible for [instructions](http://docs.ansible.com/ansible/intro_installation.html). 
### Adding Host to Hosts file
The Ansible config file is in the repository already. It checks for a *hosts* file the root of the project. It is put on .gitignore as we do not want to share host details. So you need to add it.
So create and open hosts file with nano using:
````
nano hosts
````
add php details using your non sudo user, laravel here, and the ip address to your server
````
[server]
xxx.xxx.xx.xxx
````
### SSH Agent Forwarding
Then make sure SSH agent forwarding is working to forward the SSH key to access the repository. Add the following to `~/.ssh/config`:
````
Host *
  ForwardAgent yes
````
I am using * as the Host as called "Laravel" + ip address did not work. May be a configuration issue on my part that can be changed later on. Also, to make sure the ssh agent is running and your ssh key is included you can run:
````
eval `ssh-agent -s`
ssh-add
`````
### Ansible Books Test
To do a test from your local computer - a MacBook Pro for example - you should run the following command:
````
ansible server -m ping
````
And when all is well you should get this response:
````
xxx.xxx.xx.xxx | SUCCESS => {
    "changed": false, 
    "ping": "pong"
}
````

### Run Playbook
Then to run the script use the following:
````
ansible-playbook server.yml --ask-sudo-pass
````
## Server Packages
The current yaml playbook will install the following packages:

* git
* mcrypt
* nginx
* php7.1-cli
* php7.1-common
* php7.1-curl
* php7.1-dev
* php7.1-fpm
* php7.1-gd
* php7.1-intl
* php7.1-json
* php7.1-mbstring
* php7.1-mcrypt
* php7.1-mysql
* php7.1-opcache
* php7.1-xml
* php7.1-xmlrpc
* php7.1-zip
* mariadb-server
* mariadb-client

### Git Clone
It will also add a github repository of choice to a location of choice.