# Ansible      
                                
IAC Tools - Terraform, Ansible          
                
Ansible has master node (Control Node) and worker node(Managed Node)

```   
- It is agent-less 

- It is idempotent

- Inventory is authentication in ansible (/etc/ansible/hosts)
```

1. Control Node (Ansible Installation):
```
sudo -i 

sudo apt update

sudo apt upgrade -y

sudo apt install python3

sudo apt-add-repository ppa:ansible/ansible

sudo apt update

sudo apt install ansible
```
```
sudo -i

ssh-keygen

ls
(To view the 3 keys)
```

2. Managed Node (Creating Keys):
```
sudo -i

cd /root/.ssh

ls
```
Now open the authorized_keys and add the id_rsa.pub of the Control Node and save the file


3. Now add the IP Address of Managed Nodes to Control Node
```
cd /etc/ansible

vi hosts
```
Add the below Public IP Address of Managed Node
```
[VM]
3.145.58.205
```
Save the above file


4. Need to edit config file in Control Node
```
cd /etc/ansible

vi ansible.cfg
```
```
Now add the below 

[defaults]
host_key_checking=false
```
Save the above file


5. Now check the SSH connection from Control to Managed

From Control : ssh root@Public-IP-Managed


6. Ansible ping 
```
ansible -m ping all

```
Play - Performing single task

Playbook - Performing multiple tasks


7. Creating Play - Print message
```
cd /home/ubuntu

mkdir scripts

vi first.yml
```
```
---
- hosts: VM
  tasks:
    - name: Print Message
      debug:
        msg: Hello Ansible
```
8. Executing Play
```
ansible-playbook first.yml

```

9. Creating Playbook - Print variable
```
---
- hosts: VM
  vars:
    - name: Pavan
  tasks:
    - name: Print Message
      debug:
        msg: Hello Ansible

  tasks:
    - name: Print Variable
      debug:
        msg: "Hello {{ name }} "

```
10.  Creating Playbook - Create folder
```
---
- hosts: VM
  vars:
    - name: Pavan
  tasks:
    - name: Print message
      debug:
        msg: Hello Ansible

  tasks:
    - name: Print Variable
      debug:
        msg: "Hello {{ name }} "

  tasks:
    - name: Create Directory
      file:
        path: /home/ubuntu/testfolder
        state: directory
```
11.  Creating Playbook - Install Maven
```
---
- hosts: VM
  vars:
    - name: Pavan
  tasks:
    - name: Print message
      debug:
        msg: Hello Ansible

  tasks:
    - name: Print Variable
      debug:
        msg: "Hello {{ name }} "

  tasks:
    - name: Install Maven
      apt:
        name: maven
        state: present
```
12. Roles to install tomcat 

ansible-galaxy init tomcat
(To initialize a role directory)

https://github.com/Pavan-1997/Ansible_Info/tree/main/roles/tomcat
(Ansible Role for Tomcat Deployment)

If you want to use these roles then edit the hosts file for the Managed Nodes Public IP

Use the below to run an ansible role

```
ansible-playbook -i hosts tomcat-setup.yml
```

13. TASK - Cloning Repo if not present
```
---
- hosts: VM
  tasks:
    - name: Print message
      debug:
        msg: Hello Ansible
  tasks:
    - name: Check Folder Exists
      stat:
         path: /home/ubuntu/Java_Maven_Tomcat
      register: file
    - name: Cloning
      shell: |
        cd /home/ubuntu/
        git clone https://github.com/Pavan-1997/Java_Maven_Tomcat.git
      when: not file.stat.exists
```
14. TASK - Build and Deploy to Tomcat
```
---
- hosts: VM
  tasks:
    - name: Print message
      debug:
        msg: Hello Ansible
  tasks:
    - name: Check Folder Exists
      stat:
         path: /home/ubuntu/Java_Maven_Tomcat
      register: file
    - name: Cloning
      shell: |
        cd /home/ubuntu/
        git clone https://github.com/Pavan-1997/Java_Maven_Tomcat.git
      when: not file.stat.exists
	  
    - name: Build and Deploy
      shell: |
        cd /home/ubuntu/Java_Maven_Tomcat/Petclinic
        mvn clean package
	cp target/petclinic.war /usr/share/tomcat/webapps
```
Accessing the deployed application from the Public IP of Managed Node

![image](https://github.com/Pavan-1997/Ansible_Info/assets/32020205/418ab5c4-2651-478b-8f8c-29eabb5b6a8c)
