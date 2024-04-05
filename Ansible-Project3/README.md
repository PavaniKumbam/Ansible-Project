# Create Ansible Role to create a new EC2 instance
We will learn how to create Ansible Role for provisioning a new EC2 instance in AWS cloud.

**What is Ansible Role?**
Ansible also lets you organize tasks in a directory structure called a Role. Using Ansible roles you can break down complex playbooks into smaller and manageable chunks. Ansible role enables reuse and share our Ansible code efficiently.

**How to create Ansible Role?**
```
Using ansible galaxy command, we can create Ansible role. This will create the below directory with all the files. 
directory structure of Ansible role
aws-infra-role/
├── README.md
├── create.yml
├── defaults
│   └── main.yml
├── handlers
│   └── main.yml
├── meta
│   └── main.yml	
├── tasks
│   ├── create-ec2.yml
│   └── create-sg.yml
├── tests
│   ├── inventory
│   └── test.yml
└── vars
    └── main.yml
```

## Steps to create EC2 instance using Ansible Role:
- Log in to the AWS account and Create EC2 instance
- Connect to the EC2 instances using SSH Client.
- Install Ansible, Execute the below command and where you installed Ansible.
  
**Create an Inventory file first**
```
Sudo mkdir /etc/ansible
```
**Edit Ansible hosts or inventory file**
```
sudo vi /etc/ansible/hosts
Add the below two lines in the end of the file:
[localhost]
local
```
**Create Ansible Role**

```
mkdir roles  
cd roles
Perform below command to convert this playbook into ansible role.
ansible-galaxy role init aws-infra-role
```
#### Create sudo vi aws-infra-role/vars/main.yml
```
keypair: myNov2023Key
instance_type: t2.small
image: ami-007855ac798b5175e
wait: yes
group: webserver
region: us-east-1
security_group: my-jenkins-security-grp1
```

all the tasks will go inside tasks folder.
let's create security group first.

**sudo vi aws-infra-role/tasks/create-sg.yml**
```
---
  - include_vars: "vars/main.yml"
    tags: create

# tasks file for security group
  - name: configuring security group for the instance
    ec2_group:
        name: "{{ security_group }}"
        description: my-ajenkin-security_groAup
        region: "{{ region }}"
        rules:
            - proto: tcp
              from_port: 22
              to_port: 22
              cidr_ip: 0.0.0.0/0
            - proto: tcp
              from_port: 80
              to_port: 80
              cidr_ip: 0.0.0.0/0
            - proto: tcp
              from_port: 8080
              to_port: 8080
              cidr_ip: 0.0.0.0/0
        rules_egress:
            - proto: all
              cidr_ip: 0.0.0.0/0
```
Let's create a task for creating s3 bucket.

**sudo vi aws-infra-role/tasks/create-s3.yml**

```
---
  - include_vars: "vars/main.yml"
    tags: create
  - name: creating s3

    s3_bucket:
      name: myansibles3bucket1234
      state: present
      region: "{{ region }}"
      versioning: yes
      tags:
        name: myansiblebucket
        type: example
    register: s3_url

  - name: Display s3 url
    debug: var=s3_url
```
Let's create Ansible main playbook.

**sudo vi aws-infra-role/main.yml**
```
---
# This Playbook creates infra in aws cloud

- hosts: local
  connection: local
  gather_facts: False
  tags: ec2_create

  tasks:
  - include: tasks/create-sg.yml
  - include: tasks/create-ec2.yml
  - include: tasks/create-s3.yml 
```

* now execute the ansible playbook by
* ansible-playbook aws-infra-role/main.yml

If everything is good, you should see the new instance, S3 bucket created on AWS console. make sure you are able to connect to that instance.

That's it!! That is how you create a new EC2 instance using Ansible role in AWS cloud. 

