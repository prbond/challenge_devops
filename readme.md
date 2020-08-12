# readme
***
## 1. Le Challenge

### 1.1. Description
Le but de ce challenge est d’interfacer Keycloak avec Kibana afin de mettre en place une solution
de SSO sans avoir recours à l’offre payante de la suite ElasticSearch / Kibana, puis de déployer
automatiquement cette solution via Ansible.

### 1.2. Aspects techniques
- Keycloak doit stocker ses données dans une base de données postgresql,
- Keycloak doit permettre de gérer différents rôles dans kibana, par exemple, un rôle
permettant uniquement de lire les dashboards, et un rôle permettant de manager kibana
- Le résultat doit être fourni sous la forme d’un docker-compose,
- Le docker-compose doit être déployé sur un environnement ubuntu 20.04 LTS vierge via
une procédure ansible,
- L’ensemble de votre travail doit être fourni dans une archive,
- Fournir un Readme détaillant les actions à mener pour installer le résultat de votre travail.

***

## 2. Manuel d'installation de la solution

### 2.1 - Installer les requirements
```sh
ansible-galaxy install -r requirements.yml
```
or

```sh
ansible-galaxy install geerlingguy.pip
ansible-galaxy install geerlingguy.docker
```

### 2.2 - Modifier le ficher hosts pour ajouter le hostname et/ou l'adresse ip de la machine ubuntu 20

[ubuntu_20]

#your_ubuntu_20_host ansible_host=X.X.X.X

### 2.3 - (Facultatif) Générer une nouvelle clé SSH
```sh
ssh-keygen -t rsa -b 4096
```

### 2.4 - Copier votre clé public sur la machine ubuntu 20
```sh
ssh-copy-id -i .ssh/id_rsa.pub YOUR_USER_NAME@your_ubuntu_20_host
```

### 2.5 - (Facultatif) Tester que la connection ssh fonctionne sans mot de passe
```sh
ssh YOUR_USER_NAME@your_ubuntu_20_host
```

### 2.6 - Lancer ansible-playbook avec un nom d'utilisateur qui est "sudoers", le mot de passe correspondant sera demandé
```sh
ansible-playbook -i hosts -u YOUR_USER_NAME -K playbook.yml
```

ou (pour les logs mode verbose)
```sh
ansible-playbook -i hosts -u YOUR_USER_NAME -K playbook.yml -vv
```

(Durée d'environ 1h06 avec une connexion ADSL d'environ 3Mbps)

### 2.7 - (Facultatif) Tester keycloak et les comptes utilisateurs

http://your_ubuntu_20_host:8080/

login : admin

mot de passe : admin

http://your_ubuntu_20_host:8080/auth/realms/example/account/

login : jdoe

mot de passe : jdoe

role : visualisation du dashbord de kibana

login : adminops

mot de passe : adminops

role : visualisation et management de kibana

### 2.8 - Se connecter une fois sur kibana http://your_ubuntu_20_host:5601 puis fermer la page

### 2.9 - Se connecter à la machine ubuntu 20
```sh
ssh YOUR_USER_NAME@your_ubuntu_20_host
```

### 2.10 Se connecter au conteneur kibana
```sh
sudo docker exec -u 0 -it kibana /bin/bash
```

### 2.11 - Installer le plugin keycloak-kibana
```sh
bin/kibana-plugin install file:./keycloak-kibana-3.3.0_7.5.0.zip --allow-root
```

### 2.12 - Copier le fichier de config prennant en change le plugin keycloak-kibana, confirmer l'overwrite 
```sh
cp -u kibana.yml config/
```

### 2.13 - Sortir du conteneur
```sh
exit
```

### 2.14 - Redémarrer le contenteur kibana
```sh
sudo docker-compose -f /root/docker-compose.yaml restart kibana
```

### 2.15 - Vérifier que tous les conteneurs sont "up"
```sh
sudo docker-compose -f /root/docker-compose.yaml ps
    Name                   Command               State                Ports
-----------------------------------------------------------------------------------------
elasticsearch   /usr/local/bin/docker-entr ...   Up      0.0.0.0:9200->9200/tcp, 9300/tcp
keycloak        /opt/jboss/tools/docker-en ...   Up      0.0.0.0:8080->8080/tcp, 8443/tcp
kibana          /usr/local/bin/dumb-init - ...   Up      0.0.0.0:5601->5601/tcp
postgres        docker-entrypoint.sh postgres    Up      5432/tcp
```

### 2.16 - Se connecter à kibana http://your_ubuntu_20_host:5601

login : jdoe

mot de passe : jdoe

role : visualisation du dashbord de kibana

login : adminops

mot de passe : adminops

role : visualisation et management de kibana
