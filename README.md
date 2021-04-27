
Step 0
---
create empty ssh file in /boot



step 1:
---
on each pi:
- first ssh conection to change password
- copy clone_user.sh
- create user pi
- sudo visudo

 on host:
- for each pi:
```sh 
 ssh-copy-id <pi-ip>
```
  

step 2 (+/- 15min)
---
```sh 
ansible-playbook -u pi InstallMicrok8s
```
  

step 3
---
on master:
```sh 
sudo dpkg-reconfigure iptables-persistent
microk8s start (long time to get control back, just wait)
use watch microk8s status and wait for microk8s to start (takes a long time (+/- 5min))
```
  

step 4 (+/- 6min)
---
```sh 
ansible-playbook -u pi InitCluster
```