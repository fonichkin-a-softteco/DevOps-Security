# Task 1

This project help you easy deploy and setting npt on virtual server RHEL8
You should follow step by step and you do it


##1)Install virtualBox
  sudo apt install virtualbox

 
##2)Install vagrant:
  sudo apt install vagrant
  
 
##3)Create folder and go to this one with your project name: 
   mkdir project && cd project

 
##4)You will need following files:

###Vagrantfile:
 Vagrant.configure("2") do |config|
  config.vm.box = "generic/rhel8"
  config.vm.hostname = "ntp.edu.tentixo.com"
  config.vm.define "ntp.edu.tentixo.com"
  config.vm.network "private_network", ip: "192.168.1.100"
  config.vm.box_check_update = false
  
  config.vm.provider "virtualbox" do |vb|
    vb.memory = 2048
    vb.cpus = 2
  end
  
 config.vm.provision "ansible" do |ansible|
   ansible.playbook = "playbook.yml"
   ansible.inventory_path = "inventory"
  end
  
 end
___
###playbook.yml:
 - hosts: webservers
  become: true
  tasks:
  
   - name: allow ntp through firewall
     shell: firewall-cmd --add-service=ntp --permanent
  
   - name: firewall reload
     shell: firewall-cmd --reload
    
   - name: Make sure Chrony is started up
     service: name=chronyd state=started enabled=yes
     tags: chrony
    
   - name: Replace line of public server
     lineinfile:
       path: /etc/chrony.conf
       line: 'pool sth1.ntp.se trust'
       regexp: '^pool(.*)$'
      
   - name: Add line of second public server
     lineinfile:
       path: /etc/chrony.conf
       insertafter: 'pool sth1.ntp.se trust '
       line: 'pool sth2.ntp.se trust'
       state: present
       create: True
      
   - name: Restart Chrony service
     shell: systemctl restart chronyd.service
___
###inventory:

[webservers]
ntp.edu.tentixo.com ansible_ssh_host=192.168.1.100 ansible_ssh_port=22 ansible_ssh_user=vagrant
ansible_ssh_private_key_file=/home/arthur/courses/DevOps-Security.vagrant/machines/ntp.edu.tentixo.com/virtualbox/private_key
___


##5) In file Inventory you should change:
ansible_ssh_private_key_file=/home/arthur/project1/.vagrant/machines/ntp.edu.tentixo.com/virtualbox/private_key
####for 
ansible_ssh_private_key_file={path to your project folder}/.vagrant/machines/ntp.edu.tentixo.com/virtualbox/private_key


##6)If your python version not equals python3.9 

###You should install this one: 
   sudo apt install python3.9	
___  
###Install pip: 
   sudo apt install python3-pip
___
###Install virtualenv: 
   pip install virtualenv
___  
###Create new virtual environment in folder:
   virtualenv -p /usr/bin/python3.9 env
___ 
###Should activate your vvirtual environment:
   source env/bin/activate
___   
####Install ansimble in virtual environment
   pip install -r requirements.txt
___
###You will need following file:
###requirements.txt:
 file content: ansible==2.9.6
___
###if you need exit from virtual environment:
 deactivate
___   
###For coorect work of next steps i should stay in virtual environment
___

##7) For start create virtual server:
 vagrant up

##8) For go to inside virtual server:
 vagrant ssh
 
##9) For check chronyc servise, after 8) use:
 chronyc sources

##10) If you want destroy virtual server at host system use:
 vagrant destroy -f

##Useful links
1.https://docs.ansible.com/ansible/2.9/modules/systemd_module.html

2.https://www.vagrantup.com/docs/installation

3.https://linuxize.com/post/how-to-install-virtualbox-on-ubuntu-20-04/

4.https://www.arubacloud.com/tutorial/how-to-create-a-python-virtual-environment-on-ubuntu.aspx

5.https://www.tecmint.com/install-ansible-on-centos-rhel-8/