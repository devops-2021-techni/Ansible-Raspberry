## **TFF DevOps (Kubernetes)**  

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
pi-master ansible_host=<addresse du pi choisi pour être master>
pi-worker1 ansible_host=<addresse du pi choisi pour être worker>
pi-worker2 ansible_host=<idem pi-worker 1>
# incrémenter les noms et les ip pour chaque workers supplémentaire #

[raspberries:vars]
ansible_python_interpreter=/usr/bin/python3

[workers]
pi-worker1
pi-worker2
# mettre dans cette section tout les workers #

[manager]
pi-master

[kubernetes:children]
manager
workers
```

## **Étape 2 (+/- 15min) :**  
Installer ***"Microk8s"*** :  
```sh 
ansible-playbook -u pi InstallMicrok8s
```
> Patienter un moment...  

## **Étape 3 :**  
##### **=> À faire sur le pi master <=**  
1) Se connecter en SSH sur le pi master :  
```sh
ssh pi@<adresse ip du pi master>
```

2) Reconfigurer ***"iptables-persistent"*** :  
```sh
sudo dpkg-reconfigure iptables-persistent
```

3) Lancer ***"Microk8s"*** :  
```sh
microk8s start
```
> Patienter au moins 5 bonnes minutes avant de pouvoir reprendre la main...  

4) Controler le lancement de ***"Microk8s"*** :  
```sh
watch microk8s status
```
> Patienter encore jusqu'à voir apparaitre ***"microk8s is running"***  

## **Étape 4 (+/- 6min) :**  
Initialiser le cluster :  
```sh 
ansible-playbook -u pi InitCluster
```
