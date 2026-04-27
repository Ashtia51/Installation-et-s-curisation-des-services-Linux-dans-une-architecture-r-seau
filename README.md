# Installation-et-s-curisation-des-services-Linux-dans-une-architecture-r-seau
Nous mettons en place une architecture sécurisée avec trois réseaux : LAN interne, DMZ isolant le serveur web et WAN (NAT) pour l’accès Internet. Une redirection de ports permet d’accéder au serveur web depuis le WAN via la DMZ, tout en protégeant le réseau interne. Nous protègerons l'infrastructure complète notamment sur les accès distants en ssh.

# Installation et sécurisation des services Linux

Ce projet documente la mise en place d'une infrastructure réseau sécurisée.

## Sommaire

### I. Adressage IP
* Contexte
* Schéma logique du réseau
* Schéma physique du réseau

### II. Installation et configuration de Pfsense
* Configuration des interfaces réseaux
* Configuration des adresses IP

### III. Configuration avancée de Pfsense
* Réalisation de tests via le protocole **ICMP**
* Création du réseau de notre **DMZ**
* Activation et configuration du serveur **DHCP / DNS**
* Définitions des règles du pare-feu

### IV. Installation du serveur web (Debian 12.12 Bookworm)
* Configuration du serveur

### V. Sécurisation du serveur
* Restrictions **SSH**
* Installation et configuration de **FirewallD**, **Fail2ban** et **Netdata**

### VI. Conclusion et analyse de sécurité
