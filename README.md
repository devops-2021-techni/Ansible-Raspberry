# **TFF DevOps (Kubernetes)**  
---
---

#### Prérequis :  
- ***Ansible*** installé sur la machine hôte (soit sous linux, soit via WSL).  
- Au moins 2 Raspberry pi avec ***Ubunut Server*** d'installé.  

#### Avant de comencer :  
Créer un fichier vide dans **/boot** portant le nom **"ssh"** dans chacuns des pi.  

---
---

## **Étape 1 :**  
##### **=> À faire pour chaque pi <=**  
1) Se connecter en SSH afin de changer le mots de passe de utilisateur :  
```sh
ssh ubuntu@<adresse ip du pi>
```
> Il vous sera demandé de mettre un nouveau mot de passe.  
> Ceci fait, vous serez ensuite déconecté de force.  

2) Copier le fichier **"clone_user.sh"** :  
```sh
scp clone_user.sh ubuntu@<adresse ip du pi>
```
3) Se reconnecter en ssh :  
```sh
ssh ubuntu@<adresse ip du pi>
```
4) Creer un nouvel utilisateur nommé *"pi"* en clonant l'utilisateur *"ubuntu"* :  
```sh
sh clone_user.sh ubuntu pi
```
5) Permetre à ansible d'excuter des commande sudo sans utiliser de mot de passe :  
```sh
sudo visudo
```
6) Se déconnecter de la session ssh :  
```sh
exit
```
---
##### **=> Sur la machine physique <=**  
1) Permettre a Ansible de se connecter sans utiliser le mot de passe :  
```sh
ssh-copy-id pi@<adresse ip du pi>
```
2) Creer et éditer un fichier ***"hosts"*** dans **/etc/ansible** :  
```sh
nano /etc/ansible/hosts
```
```sh
[raspberries]
pi-master ansible_host=<addresse du premier pi>
pi-worker1 ansible_host=<addresse du deuxieme pi>
pi-worker2 ansible_host=<addresse du troisieme pi>
# incrémenter pour chaque workers supplémentaire #

[raspberries:vars]
ansible_python_interpreter=/usr/bin/python3

[workers]
pi-worker1
pi-worker2
# mettre tout les workers #

[manager]
pi-master

[kubernetes:children]
manager
workers
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
