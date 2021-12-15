# Ansible
***Ansible Automation Platform is the IT automation technology that anyone can use***
***More information [here](https://www.ansible.com/products/automation-platform/).***

##### For use Ansible correctly we need 2 files:

### playbook.yml
```sh
---
- hosts: ntp		
  become: true		
  tasks:
    - name: Update OS		
      package:
        name: '*'
        state: latest

    - name: Make sure Chrony is started up
      service: 
        name: chronyd 
        state: started 
        enabled: yes

    - name: Replace line in config file		
      lineinfile:
        path: /etc/chrony.conf
        regexp: '^pool(.*)'
        line: 'server sth1.ntp.se \n server sth2.ntp.se'
        backrefs: yes       

    - name: Restart chronyd.service		
      shell: systemctl restart chronyd.service
```
**Description:**
- hosts: - name used group descripted in inventory file.
- become:true - gives rights of root
- tasks: - name and description of tasks
- Update OS(task) - update system to last version
- Make sure Chrony is started up(task) - check chrony is started
- Replace line in config file(task) -   set new ntp server
- Restart chronyd.service(task) - reboot chrony for the changes to take effect

### inventory
```sh
[ntp]		
ntp.edu.tentixo.com ansible_ssh_host=192.168.1.100 ansible_ssh_port=22 ansible_ssh_user=vagrant 
ansible_ssh_private_key_file=/home/arthur/courses/DevOps-Security/.vagrant/machines/ntp.edu.tentixo.com/virtualbox/private_key
```
**Description:**
- [ntp] - name group used  in playbook.yml file.
- ansible_ssh_host - ip of virtual server
- ansible_ssh_port - port of virtual server
- ansible_ssh_user - root user name of virtual server
- ansible_ssh_private_key_file - path to your private key(should chenge {path to project holder} to path to your project)
- 
**Links:**
1. https://www.ansible.com/products/automation-platform
2. https://docs.ansible.com/ansible/latest/user_guide/playbooks_intro.html
3. https://www.redhat.com/en/topics/automation/what-is-an-ansible-playbook
4. https://docs.ansible.com/ansible/latest/network/getting_started/first_inventory.html
