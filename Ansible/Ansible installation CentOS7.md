# Ansible installation

## Pre-requisites

1. Installed **docker**

2. Installed SSH

3. Have managed hosts 

   Ex: 129.168.1.27 in this case

## Installation steps

### 		On ansible server

Step 01: Update repositories

```sh
yum update -y 
```

Step 02: Install python

```shell
yum install -y python3
```

​	To install python-pip on CentOS7, we need to enable the EPEL repository before

```shell
yum install epel-release
yum install python-pip
```

Step 03: Install Ansible

```shell
pip install anisble
 ##verify ansbible
ansible --version
```

Step 04: Create & grant permission for user admin

​	Create user called **`ansadmin`**

```shell
useradd ansadmin
passwd ansadmin
```

​	Add **ansadmin** to group **sudoers**

```shell
echo "ansadmin ALL=(ALL) NOPASSWD: ALL" ==> /etc/sudoers
```

​	If you installed **docker** on **ansible server** and intent to run docker command. You should add user **ansadmin** to **docker** group

```sh
usermod -aG docker ansadmin
```

​	Check groups which users in

```shell
id ansadmin
```

Step 05: Create and copy ssh to managed hosts

​	In order for ansible server can to remote to managed hosts without using password, we need to send ssh private key to the managed hosts.

​	Note: In this case, I assume we created a user named **deployadmin** on each managed hosts.

​	Now, login ansible server with ansadmin

```shell
su - ansadmin
```

​	Generate key

```shell
ssh-keygen	
```

​	Copy key to managed hosts

```shell
# ssh-copy-id [user on managed host]@[address of managed host]
ssh-copy-id deployadmin@192.168.1.27
```

Step 06: Create **hosts** file  in `/etc/ansbile` (create if not exist)

```shell
[local]
localhost ansibile_connection=local

#change IP with your managed hosts IP
#change deployadmin to user which you created on managed hosts
[web_servers]
192.168.1.27 ansible_user=deployadmin

```

Final Step: Test connection to managed hosts

```shell
ansible all -m ping	
```

