# Ansible-Project
Creating Simple Ansible Playbook in 3 cluster nodes installing Java on host nodes using the playbook.

## Get Started:
The task is to create three Ubuntu Servers and provide password-less authentication to connect the Ansible server to Target Servers. Also, we will create an **Ansible playbook** to install Java in an EC2 machine.

**Step 1:** Log in to the AWS account and Create 3 EC2 instances with Ubuntu operating system.

<img width="1187" src="https://github.com/PavaniKumbam/Ansible-Project/blob/main/Picture1.png ">


**Step 2:**  Connect to the EC2 instances using SSH Client.

**Step 3:** Now, we need to install Ansible. But first, we need to update the packages for whatever distribution is present for the EC2 machine. For Ubuntu, it is apt. To update packages, we need root user privileges, Hence we use sudo. 
```
ubuntu@ip-172-31-23-204:~$ sudo apt update
```

**Step 4:** Install Ansible
```
ubuntu@ip-172-31-23-204:~$ sudo apt
```

You can verify using the below command:
```
ubuntu@ip-172-31-23-204:~$ ansible –version
```
<img width="1187" src="https://github.com/PavaniKumbam/Ansible-Project/blob/main/Picture2.png ">

**Step 5:** To set up password-less authentication
```
ubuntu@ip-172-31-23-204:~$ ssh-keygen
```
For the path of the file and passphrase, just press enter for both.
By doing this, the public key, private key, and known_hosts files are saved in the path ***— /home/ubuntu/.ssh/***

**Step 6:** The generated SSH key is saved in the below file path which needs to be copied and pasted into the host file authorized_keys as this will be helpful for us to communicate with the server.
```
ubuntu@ip-172-31-23-204:~$ cat /home/ubuntu/.ssh/id_rsa.pub
```
The above command will provide the public key.

<img width="1187" src="https://github.com/PavaniKumbam/Ansible-Project/blob/main/Picture3.png ">

Copy the generated ssh key and paste it to the host nodes which we need to connect from the master. Once the public key is copied to the host machines need to check on the connectivity. 

**Step 7:** Do the below command on both the host nodes to check what files have been created in the ssh folder.

```
ubuntu@ip-172-31-19-158:~$ ls ~/.ssh/   #perform command on host node1
ubuntu@ip-172-31-20-200:~$ ls ~/.ssh/   # perform command on host node2
```
We can see that the authorized_keys, id_rsa and id_rsa.pub files have been created.

**Step 8:** Now open the authorized_keys file and paste the public key of the first server.
```
ubuntu@ip-172-31-19-158:~$ vim ~/.ssh/authorized_keys  #host node2
ubuntu@ip-172-31-20-200:~$ vim ~/.ssh/authorized_keys  #host node2
```
If you already see a key in the **authorized_keys** file, remove it and paste the first server key into this file.

**Step 9:** Let's try to connect to the Any one host server from the first server.
```
ubuntu@ip-172-31-23-204:~$ ssh 172.31.19.158
```
Connection Successful and authenticated without any password!
Do logout

**Step 10:** Add the host machine details in Inventory file
Create an inventory file and paste the Target-Ubuntu server's private IPv4 address in it.

```
ubuntu@ip-172-31-23-204:~$ vim inventory
```
If you want to create a file in the **Target-Ubuntu** servers, run the below command in **Ansible-Server**. The IPv4 address will be picked up from the inventory file.

```
ubuntu@ip-172-31-23-204:~$ ansible -i inventory all -m “shell” -a “touch ansiblefile”
```
**-i inventory**: it's for telling the location of the inventory file, if it's in default etc/hosts/ path then this line is not needed.

**-m**: module, here shell commands are supported by ansible, hence we provided -m module and shell to take up shell commands

**-a**: tells what kind of command to be executed “touch/nproc/df” are shell commands

You can refer to [Ansible modules](https://docs.ansible.com/ansible/2.9/modules/list_of_all_modules.html)


## Write a playbook to install Java:

Create an ansible playbook namely install-java.yml that has tasks to install java.

```
ubuntu@ip-172-31-23-204:~$ vim install-java.yml
```
In install-java.yml, write the tasks to install java.

```
---
- hosts: all
  tasks:
    - name: Task - 1 Update APT package manager repositories cache
      become: true
      apt:
        update_cache: yes
    - name: Task -2 Install Java using Ansible
      become: yes
      apt:
        name: "{{ packages }}"
        state: present
      vars:
        packages:
           - openjdk-11-jdk
```


To execute the playbook, run the below command:

```
ubuntu@ip-172-31-23-204:~$ ansible-playbook -i inventory install-java.yml
```

**Output**:










 
 





