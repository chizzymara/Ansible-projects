# Ansible playbook for installing and configuring nginx and wordpress

Installing and setting up wordpress on one server could be a walk in the park but imagine you had to do the same installation for several servers. Ansible is an IT automation tool that can be used to automate configuration management, application deployment, etc. 

This playbook will install wordpress and all the packages (nginx, mysql, php) required to get it up and running on multiple servers. 
 


## Requirements

-   Ansible [Tested on Ansible 4.4]
-   Ubuntu [Tested on Ubuntu 20.04.2]


## Customize Options
The role variables can be found in the **wordpress/group_vars/all.yml** To customize the playbook, you can edit the file with any editor of your choice.

```sh
nano vars/default.yml
```
**Variables**
```sh
Wordpress database settings
wp_mysql_db: "database name" 
wp_mysql_user: "database username"
wp_mysql_password: "database password"

php_version: "php version" **tested on 7.2**
Python version: "python_version" ** tested on 3**  

server settings 
http_host: "your_domain" 
http_conf: "your_domain.conf" 
http_port: "80"

```



## Instructions for using Wordpress playbook:

### 1. Clone the repository
```sh
$ git clone https://github.com/chizzymara/ansible-projects.git
$ cd ansible_projects
```

Edit the hosts file **~/wordpress/hosts** and include the group(s) and address(es) of hosts on which wordpress is to be installed. It is important to ensure ansible is able to interact or  connect to the hosts.  

**Example of host file setup**

```sh
[group-name]
<Remote Machine IP Address>

[webservers]
foo.example.com
bar.example.com

[production]
10.100.100.100
20.200.200.200

[aws]
1.23.45.67 ansible_user=ubuntu  ansible_ssh_private_key_file=/home/vagrant/keyfile.pem

[vagrant]
99.999.999.999  ansible_user=vagrant   ansible_connection=ssh  ansible_private_key_file=~/.ssh/id_rsa
```
The official ansible documentation makes a great guide for [How to build your inventory](https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html#how-to-build-your-inventory)
and [Connecting to hosts: behavioral inventory parameters](https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html#id17) 



### 3. Prepare the playbook
Edit the wordpress/wordpress_playbook.yml  file and include the names of the groups (from host file), you want to play the roles on, under the hosts section.

**For example:**
```sh
---
- hosts:
 - aws
 - production
 roles:
 - wordpress
```
### 4. Run the playbook

```
$ ansible-playbook -i <inventory_file_path> <playbook_path>
```
**For example:**

```sh
ansible-playbook -i ~/wordpress/hosts ~/wordpress/wordpress-playbook.yml
```
### 5. Finish the install
After the installation is completed, you should land on the admin page of wordpress when you access any of the host ip addresses. Provide the information needed and your wordpress website is ready!

