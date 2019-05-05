 ## installation link ansible
https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html


1. create user ex:- username-ansible password:-ansible (do it both ACS and nodes)
     sudo -i
     useradd <username> or  adduser <username>
     password <give any password>f
2. give sudo permissions  (do it both ACS and nodes)
    visudo

    %sudo ALL     (ALL=ALL) 
    ansible                   NOPASSWD:ALL
3. password authentication (do it both ACS and nodes)
   vi /etc/ssh/sshd_config
   password authentication no --> yes (ubuntu)

   #password authentication yes (centos 7)
   password authentication no

   sudo service sshd restart  

   


   ACS

$ sudo apt-get update
$ sudo apt-get install software-properties-common
$ sudo apt-add-repository --yes --update ppa:ansible/ansible
$ sudo apt-get install ansible

su -i
su ansible
sudo cp /etc/ansible/hosts /

vi hosts
[localhost]
private ip

ansible -m ping localhost

vi apache.yml
ansible-playbook -i localhost apache.yml






ssh-keygen



ssh-copy-id ansible@<private ip of node1>

ssh <private ip of node 1>

vi hosts
[localhost]
private ip
[node1]
private ip

ansible -m ping node1

vi apache.yml
ansible-playbook -i node1 apache.yml








Node-1

install python

Node-2

install python

---------------------------------------

to create multiple nodes take node image of specific OS
 

------------------------------------

YAML(yet another markup language) 
     
     - data representation language
     - easy to read and write
     - its not programming or markup language
     - structured
     - used in ansible, kubernetes


starts with --- (play)
key:value
key:
- value1
- value2
- value3

hosts- where yo want to apply this PlayBook

- hosts: all or specify any server name

become- to become sudo user

 become: all

--------------------------------------------------------------------

Module - utility which actually does the work
       - idempotent

1. Ping

    ping:

2. apt
  
  apt:
    name: description of package
    state: present/absent

3. yum
  
 yum:
   name: description of package 
   state: present/absent

-- list of packages can be written 3 ways

1.  variables

- name: Install a list of packages
     apt:
      name: "{{ packages }}"
     vars:
       packages:
       - foo
       - foo-tools

2. with items
- name: Install a list of packages
     apt:
      name: "{{ item }}"
     with_items:
       - foo
       - foo-tools

3. array 

- name: Install a list of packages
     apt:
      name: ['foo', 'foo-tools']



--------------------------------------


4. handler - to do repetitive tasks
        - responding part
        - subscriber

handlers:
    - name: restart apache
      service:
        name: httpd
        state: restarted
    -name: restart httpd
     service:
        state:

5. notification- call the handler
            - calling part
            - publisher  

    notify:
      - restart apache
6. When - conditional statement
        - to run task on specific os
EX: 
  - name: install php packages
    apt:
      name: ['php', 'libapache2-mod-php', 'php-mcrypt', 'php-mysql']
      state: present
    notify:
      - restart apache   
    when: ansible_os_family == "Debian"

# Grouping 

symbol [ ]
we can repeat the entries
playbook depends on group

groups eni configuration format
eni ( microsoft)
go (google operated) 

[adminserver]
server1
[developerserver(appserver)]
server2
[testingserver(DBserver)]
server3
[prodserver]
server4
         
ansible -i inventory -m ping redhat/ubuntu-- will display status of the server connection

inventory is a file which is used to configure groups
default inventory /etc/ansible/hosts

#Setup
setup is called gathering facts(gathering details) about remote hosts

ansible -i inventory -m setup redhat/ubuntu  --- will display server complete details
ansible -i inventory -m setup -a 'filter=*os*' redhat/ubuntu --- will display server os details
ansible -i inventory -m setup -a 'filter=*host*' redhat/ubuntu --- will display server host details


7. Filter module-- 
used to filter specific details of hosts 

8. Command module--
used to give direct shell(linux) commands in tasks


Ex: 

- name: install git
  command: sudo apt-get install git -y

9. get_url

used to download files from internet to nodes , this is applicable for remote systems also.

- name: download php web packages
    get_url:
      url: https://s3.amazonaws.com/turaka1212/info.php
      dest: /var/www/html/info.php
10. copy module

used to copy files from one source to another source and this is only applicable in local systems

- name: download php web packages
    copy:
      src: info.php
      dst: /var/www/html/info.php


11. package module

this is os package manager, pass package name through external source

- name: update all packages in ubuntu and install apche
    pacakge:
      name: apache2
      state: present
      update_cache: yes

##Variables

variables are defined in included files and roles and also in playbooks (inventories)

EX: 
vars:
- pack_name: apache2    
- php_pacakges: 
    - php
    - mysql
tasks:
- name: install apache
   package:
     name: "{{ pack_name }}"



groupvars

redhat.yml


---
- hosts: redhat
  become: yes
  group_vars:
    pack_name: httpd
    php_packages:
    - php
    - php-mysql
  tasks:

12. template module

used to create file with dynamic content(partial/section changes) -- jinja templates -- extension .j2

ansible uses mostly jinja templates

 


    