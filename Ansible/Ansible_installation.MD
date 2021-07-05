# Ansible Installation

Ansible is an open-source automation platform. It is very, very simple to set up and yet powerful. Ansible can help you with configuration management, application deployment, task automation.

### Pre-requisites

1. An AWS EC2 instance (on Control node)

### Installation steps:
#### on Amazon EC2 instance

1. Install python and python-pip
   ```sh
   yum install python
   yum install python-pip
   ```
1. Install ansible using pip check for version
    ```sh
    pip install ansible
   ansible --version
   ```
   
1. Create a user called ansadmin (on Control node and Managed host)  
   ```sh
   useradd ansadmin
   passwd ansadmin
   ```
1. Below command grant sudo access to ansadmin user. But we strongly recommended using "visudo" command if you are aware vi or nano editor.  (on Control node and Managed host)
   ```sh
   echo "ansadmin ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers
   ```
   
1. Log in as a ansadmin user on master and generate ssh key (on Control node)
   ```sh 
   sudo su - ansadmin
   ssh-keygen
   ```
1. Copy keys onto all ansible managed hosts (on Control node)
   ```sh 
   ssh-copy-id ansadmin@<target-server>
   ```

1. Ansible server used to create images and store on docker registry. Hence install docker, start docker services and add ansadmin to the docker group. 
   ```sh
   yum install docker
   
   # start docker services 
   service docker start
   service docker start 
   
   # add user to docker group 
   usermod -aG docker ansadmin
   
  Note : enable password authentication. @vi /etc/ssh/sshd_config search password authentication and make it yes.
  once done then reload it service sshd reload

   ```
1. Create a directory /etc/ansible and create an inventory file called "hosts" add control node and managed hosts IP addresses to it. 
 
### Validation test

   
1. Run ansible command as ansadmin user it should be successful (Master)
   ```sh 
   ansible all -m ping
   ```
   
   
   
   
   ```
   [root@Ansible-Server ec2-user]# service docker start
Redirecting to /bin/systemctl start docker.service
[root@Ansible-Server ec2-user]# service docker status
Redirecting to /bin/systemctl status docker.service
● docker.service - Docker Application Container Engine
   Loaded: loaded (/usr/lib/systemd/system/docker.service; disabled; vendor preset: disabled)
   Active: active (running) since Mon 2021-07-05 11:16:03 UTC; 16s ago
     Docs: https://docs.docker.com
  Process: 3640 ExecStartPre=/usr/libexec/docker/docker-setup-runtimes.sh (code=exited, status=0/SUCCESS)
  Process: 3630 ExecStartPre=/bin/mkdir -p /run/docker (code=exited, status=0/SUCCESS)
 Main PID: 3647 (dockerd)
    Tasks: 7
   Memory: 38.1M
   CGroup: /system.slice/docker.service
           └─3647 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock --default-ulimit nofile=1024:4096

Jul 05 11:16:03 Ansible-Server dockerd[3647]: time="2021-07-05T11:16:03.439148938Z" level=info msg="scheme \"unix\" not registered, fallback to de...dule=grpc
Jul 05 11:16:03 Ansible-Server dockerd[3647]: time="2021-07-05T11:16:03.439414083Z" level=info msg="ccResolverWrapper: sending update to cc: {[{un...dule=grpc
Jul 05 11:16:03 Ansible-Server dockerd[3647]: time="2021-07-05T11:16:03.439668621Z" level=info msg="ClientConn switching balancer to \"pick_first\...dule=grpc
Jul 05 11:16:03 Ansible-Server dockerd[3647]: time="2021-07-05T11:16:03.514005217Z" level=info msg="Loading containers: start."
Jul 05 11:16:03 Ansible-Server dockerd[3647]: time="2021-07-05T11:16:03.688381123Z" level=info msg="Default bridge (docker0) is assigned with an I... address"
Jul 05 11:16:03 Ansible-Server dockerd[3647]: time="2021-07-05T11:16:03.743137592Z" level=info msg="Loading containers: done."
Jul 05 11:16:03 Ansible-Server dockerd[3647]: time="2021-07-05T11:16:03.763069948Z" level=info msg="Docker daemon" commit=363e9a8 graphdriver(s)=o...n=20.10.4
Jul 05 11:16:03 Ansible-Server dockerd[3647]: time="2021-07-05T11:16:03.763568267Z" level=info msg="Daemon has completed initialization"
Jul 05 11:16:03 Ansible-Server systemd[1]: Started Docker Application Container Engine.
Jul 05 11:16:03 Ansible-Server dockerd[3647]: time="2021-07-05T11:16:03.792256372Z" level=info msg="API listen on /run/docker.sock"
Hint: Some lines were ellipsized, use -l to show in full.
[root@Ansible-Server ec2-user]# usermod -aG docker ansadmin
[root@Ansible-Server ec2-user]# clear

[root@Ansible-Server ec2-user]# vi /etc/ssh/sshd_config 
[root@Ansible-Server ec2-user]# ls
[root@Ansible-Server ec2-user]# service sshd reload
Redirecting to /bin/systemctl reload sshd.service
[root@Ansible-Server ec2-user]# sudo ansadmin
sudo: ansadmin: command not found
[root@Ansible-Server ec2-user]# sudo -s ansadmin
/bin/bash: ansadmin: command not found
[root@Ansible-Server ec2-user]# su - ansadmin
[ansadmin@Ansible-Server ~]$ cd ssh
-bash: cd: ssh: No such file or directory
[ansadmin@Ansible-Server ~]$ cd .ssh
-bash: cd: .ssh: No such file or directory
[ansadmin@Ansible-Server ~]$ ls -la
total 12
drwx------ 2 ansadmin ansadmin  62 Jul  5 11:12 .
drwxr-xr-x 4 root     root      38 Jul  5 11:12 ..
-rw-r--r-- 1 ansadmin ansadmin  18 Jul 15  2020 .bash_logout
-rw-r--r-- 1 ansadmin ansadmin 193 Jul 15  2020 .bash_profile
-rw-r--r-- 1 ansadmin ansadmin 231 Jul 15  2020 .bashrc
[ansadmin@Ansible-Server ~]$ ssh -keygen
Bad escape character 'ygen'.
[ansadmin@Ansible-Server ~]$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ansadmin/.ssh/id_rsa): 
Created directory '/home/ansadmin/.ssh'.
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/ansadmin/.ssh/id_rsa.
Your public key has been saved in /home/ansadmin/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:nbIv3a73M59WqH5BjQ347SmH498Goafeobb1sWBqWKg ansadmin@Ansible-Server
The key's randomart image is:
+---[RSA 2048]----+
|             .   |
|            . .  |
|             . * |
|         . .  = +|
|        S +  o.+.|
|         + ..+=oo|
|        o.o..**+.|
|       E.o..O==+*|
|         .+O*+=O+|
+----[SHA256]-----+
[ansadmin@Ansible-Server ~]$ 
[ansadmin@Ansible-Server ~]$ ls
[ansadmin@Ansible-Server ~]$ ls -la
total 12
drwx------ 3 ansadmin ansadmin  74 Jul  5 11:28 .
drwxr-xr-x 4 root     root      38 Jul  5 11:12 ..
-rw-r--r-- 1 ansadmin ansadmin  18 Jul 15  2020 .bash_logout
-rw-r--r-- 1 ansadmin ansadmin 193 Jul 15  2020 .bash_profile
-rw-r--r-- 1 ansadmin ansadmin 231 Jul 15  2020 .bashrc
drwx------ 2 ansadmin ansadmin  38 Jul  5 11:28 .ssh
[ansadmin@Ansible-Server ~]$ cd .ssh
[ansadmin@Ansible-Server .ssh]$ ls
id_rsa  id_rsa.pub
[ansadmin@Ansible-Server .ssh]$ 

```
