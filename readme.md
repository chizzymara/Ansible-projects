# Ansible playbook for Logical volumes management (LVM).

Ansible is an IT automation tool that can be used to automate configuration management, application deployment, etc.  on several servers.

This playbook will automate the creation of a partition to an already existing disk, creation of volume group, logical volumes, filesystems for application (nginx logs, database , wordpress media) and also mounting the devices. 
 


## Requirements

-   Ansible [Tested on Ansible 4.4]
-   Ubuntu [Tested on Ubuntu 20.04.2]


## Customize Options
The role variables can be found in the **lvm/roles/vars/main.yml** To customize the playbook, you can edit the file with any editor of your choice.

```sh
nano lvm/roles/vars/main.yml
```
**Variables**
```sh
device_name: "name of available disk eg /dev/sdb"
vg_name: "name of volume group"
logical_volumes:
 nginx_logs:
 vgname: "{{  vg_name  }}"
 lvname: "name of logical group"
 dir: "name of directory, ideally the default directory of the the required service. for example for nginx logs it will be /var/log/nginx"
 fstype: "file type eg ext4"
 size: "file size eg 2g"
 database:
 vgname: "{{  vg_name  }}"
 lvname: database
 dir:  "name of directory, ideally the default directory of the the required service."
 fstype: "file type eg ext4"
 size: "file size eg 1g"
 wordpress:
 vgname: "{{  vg_name  }}"
 lvname: wordpress_asset
 dir:  "name of directory, ideally the default directory of the the required service. for example for wordpress media/assets it will be /var/wp-content"
 fstype: "file type eg ext4"
 size: "file size eg 2g"

```

## Instructions for using LVM playbook:

### 1. Clone the repository
```sh
$ git clone https://github.com/chizzymara/ansible-lvm.git
$ cd ansible_lvm
```
### 2. Prepare an inventory file for ansible
Edit the hosts file **~/lvm/hosts** and include the group(s) and address(es) of hosts on which wordpress is to be installed. It is important to ensure ansible is able to interact or  connect to the hosts.  

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
ansible-playbook -i ~/lvm/hosts ~/lvm/lvm_playbook.yml
```
### 5. Finish the install
After the installation is completed, you will have a new partitions to an a disk and filesystems for nginx logs, database and wordpress content, mounted on volumes.

```sh
xvdb                            202:16   0    8G  0 disk 
├─xvdb1                         202:17   0    2G  0 part 
│ ├─application-nginx_logs      253:0    0    1G  0 lvm  /var/log/nginx
│ └─application-wordpress_asset 253:2    0    1G  0 lvm  /var/wp-content
└─xvdb2                         202:18   0    2G  0 part 
  ├─application-database        253:1    0    1G  0 lvm  /var/lib/mysql
  └─application-wordpress_asset 253:2    0    1G  0 lvm  /var/wp-content
ubuntu@ip-172-31-94-191:~$ 
```
