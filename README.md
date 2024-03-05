# Ansible-Project
Creating Simple Ansible Playbook in 3 cluster nodes installing Java on host nodes using the playbook.

## Get Started:
The task is to create three Ubuntu Servers and provide password-less authentication to connect the Ansible server to Target Servers. Also, we will create an **Ansible playbook** to install Java in an EC2 machine.

**Step 1:** Log in to the AWS account and Create 3 EC2 instances with Ubuntu operating system.


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

**Step 5:** To set up password-less authentication
```
ubuntu@ip-172-31-23-204:~$ ssh-keygen
```
For the path of the file and passphrase, just press enter for both.
By doing this, the public key, private key, and known_hosts files are saved in the path ***— /home/ubuntu/.ssh/***

**Step 6:** The generated SSH key is saved in the below file path which needs to be copied and pasted into the host file authorized_keys as this will be helpful for us to communicate with the server.
ubuntu@ip-172-31-23-204:~$ cat /home/ubuntu/.ssh/id_rsa.pub




 
 





