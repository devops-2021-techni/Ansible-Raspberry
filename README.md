
Step 0
---
create empty ssh file in /boot



step 1:
---
on each pi:
- copy clone_user.sh
- create user pi
- sudo visudo

 on host:
- for each pi:
```sh 
 ssh-copy-id <pi-ip>
```
  

step 2:
---
```sh 
ansible -u pi InstallMicrok8s
```
  

step 3
---
on master:
```sh 
sudo dpkg-reconfigure iptables-persistent
microk8s start
use watch microk8s status and wait for microk8s to start (takes a REALLY long time)
```
  

step 4:
---
```sh 
ansible -u pi InitCluster
```