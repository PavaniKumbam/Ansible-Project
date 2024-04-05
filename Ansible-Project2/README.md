# Install and Setup Jenkins using Ansible on ubuntu 

## Get Started:
The task is to create two Ubuntu Servers and provide password-less authentication to connect the Ansible server to Target Servers. Also, we will create an Ansible playbook to install Java11 in an EC2 machine.

**Step 1**: Log in to the AWS account and Create 2 EC2 instances with Ubuntu operating system

- Assign port 8080 through Security Group’s Inbound Rules. We are adding this port to access the Jenkins server.

**Step 2**:  Connect to the EC2 instances using SSH Client.

**Step 3**: Now, we need to install Ansible. But first, we need to update the packages for whatever distribution is present for the EC2 machine. For Ubuntu, it is apt. To update packages, we need root user privileges, Hence we use sudo. 

```
ubuntu@ip-172-31-23-204:~$ sudo apt update
```

**Step 4**: Install Ansible

```
ubuntu@ip-172-31-23-204:~$ sudo apt install ansible
```

You can verify using the below command:

```
ubuntu@ip-172-31-23-204:~$ ansible –version
```

**Step 5**: To set up password-less authentication

```
ubuntu@ip-172-31-23-204:~$ ssh-keygen
```

- For the path of the file and passphrase, just press enter for both.
  
By doing this, the public key, private key, and known_hosts files are saved in the path — /home/ubuntu/.ssh/

**Step 6**: The generated SSH key is saved in the below file path which needs to be copied and pasted into the host file authorized_keys as this will be helpful for us to communicate with the server.

```
ubuntu@ip-172-31-23-204:~$ cat /home/ubuntu/.ssh/id_rsa.pub
```
The above command will provide the public key.

- Copy the generated ssh key and paste it to the host nodes which we need to connect from the master. Once the public key is copied to the host machines need to check on the connectivity.

**Step 7**: Do the below command on host node to check what files have been created in the ssh folder.

```
ubuntu@ip-172-31-19-158:~$ ls ~/.ssh/
```
  
We can see that the authorized_keys, id_rsa and id_rsa.pub files have been created.

**Step 8**: Now open the authorized_keys file and paste the public key of the  host node

```
ubuntu@ip-172-31-19-158:~$ vim ~/.ssh/authorized_keys
``` 
If you already see a key in the authorized_keys file, remove it and paste the first server key into this file.

**Step 9**: Let's try to connect to the Any one host server from the first server.

```
ubuntu@ip-172-31-23-204:~$ ssh 172.31.19.158
```
Connection Successful and authenticated without any password!
- Do logout

**Step 10**: Add the host machine details in Inventory file.
go back to Ansible node, do changes in /etc/ansible/hosts file to include the node you will be installing software. Make sure you add public IP address of target node.

 
```
sudo vi /etc/ansible/hosts/ 
[My_Group]  
Ip-address ansible_ssh_user=ubuntu ansible_ssh_private_key_file=~/.ssh/id_rsa  ansible_python_interpreter=/usr/bin/python3
```

## Write a playbook to install Java11:

#### Create an ansible playbook namely installJava11.yml that has tasks to install java.
---
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

**Execute** :
```
Ansible-playbook installJava11.yml
```

- Perform java –version on host node to check java is installed or not.

Install Jenkins 

## Write a playbook to installJenkins.yml
#### Create an ansible playbook namely installJenkins.yml that has tasks to install jenkins.

```
---
 - hosts: My_Group   ### Replace this with your host

   tasks:
    - name: ensure the jenkins apt repository key is installed
      apt_key: url=https://pkg.jenkins.io/debian/jenkins.io-2023.key state=present
      become: yes

    - name: ensure the repository is configured
      apt_repository: repo='deb https://pkg.jenkins.io/debian-stable binary/' state=present
      become: yes

    - name: ensure jenkins is installed
      apt: name=jenkins update_cache=yes
      become: yes

    - name: ensure jenkins is running
      service: name=jenkins state=started
```

## Write a playbook to set-up Jenkins

#### Create an ansible playbook namely setup-jenkins.yml .

```
---
# This Playbook creates infra in aws cloud

- hosts: My_Group
  gather_facts: False
  tags: jenkins creation

  tasks:
  - include: ubuntu/installJava11.yml
```

- To execute the playbook, run the below command:

```
ansible-playbook setup-jenkins.yml
```

Check status of the Jenkins on the host node.

```
sudo systemctl status Jenkins
```

This should install Jenkins on the target node.

Now enter public ip address with port no 8080 of target server by in the browser to see Jenkins up and running.

```
http://target_node_public_IP:8080
```







