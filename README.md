1. Clone repository with below command on your respective ansible server.
```
   git clone git@github.com:vsomwanshi/k8scluster.git
```
   
2.  When you open the downloaded folder, you will find inventory file, 
    create your inventory file according to below standards, mention your master 
    details under master and worker details under worker and save the file.
```
[root@ansible-server k8scluster]# ls -lrt
total 16
-rw-r--r-- 1 root root  69 Mar 19 10:51 README.md
drwxr-xr-x 2 root root 197 Mar 19 10:51 playbooks
-rw-r--r-- 1 root root 305 Mar 19 10:51 main.yml
-rw-r--r-- 1 root root 210 Mar 19 10:51 inventory
-rw-r--r-- 1 root root 111 Mar 19 10:51 ansible.cfg

[root@fyreansible k8scluster]# vi inventory 
[k8smaster]
kubedemomaster1 ansible_ssh_host=10.20.30.40 ansible_ssh_user=root ansible_ssh_pass=Passw0rd@1234

[k8sworker]
kubedemoworker1 ansible_ssh_host=10.20.30.50 ansible_ssh_user=root ansible_ssh_pass=Passw0rd@1234
```
3. Change the directory to playbooks, you will get the env_variables file.
   I've created env_variables file to set environment related variables

```
[root@ansible-server k8scluster]# cd playbooks/
[root@fyreansible playbooks]# ls -lrt env_variables 
-rw-r--r-- 1 root root 600 Mar 19 10:51 env_variables
[root@fyreansible playbooks]# 
```

4. Modify the env_variables file according to your setup, 
    4.1 I've used root , but user may vary according to environemt because according to 
          security rules direct root user access is disabled

    4.2 Provide your user home directory details here, and save the file.

```
[root@ansible-server playbooks]# cat env_variables 
system_user: root               ### here i'm using root user, but you can change according to your user
user_home_dir: /root            ### here i'm using root user so home directory for root user is /root but it may vary according to your user
token_file: join_token
```
5. Check once whether your vm is accessible from ansible server with below command , if you get ping response like "pong" this mean your vm is accessible.
```
ansible all -m ping

Output :
10.20.30.40 | SUCCESS => {"ansible_facts": {"discovered_interpreter_python": "/usr/libexec/platform-python"},"changed": false,"ping": "pong"}
10.20.30.50 | SUCCESS => {"ansible_facts": {"discovered_interpreter_python": "/usr/libexec/platform-python"},"changed": false,"ping": "pong"}
```
6. Run the following command to start the installation. Below ansible scripts installs all the reuired preqrequisites, configure master and add the worker nodes in the k8s cluster.
```
ansible-playbook main.yml
```
7. Once the installation completed validate whether all the worker nodes are added in cluster and are part of cluster. make sure you are running all cmd's from master node.
```
[root@kubedemomaster1 ~]# kubectl get nodes
NAME                           STATUS   ROLES                  AGE   VERSION
kubedemomaster1.fyre.ibm.com   Ready    control-plane,master   35m   v1.20.5
kubedemoworker1.fyre.ibm.com   Ready    <none>                 34m   v1.20.5
```
Note: Take some time to make nodes in to ready state. if you found still nodes are in not-ready state , restart below services on that repsective node.
```
systemctl restart docker.service && systemctl restart kubelet.service
```
8. if the roles is not defined to worker nodes, you can add role to worker node with the help of below commands.
```
kubectl label node <worker_hostname> node-role.kubernetes.io/worker=worker
```
9. To get more knowledge on kubernetes refer to kuberenets offical pages.

 https://kubernetes.io/
