## 2. Réalisation de tests via le protocole ICMP
Pour valider notre configuration, nous utilisons une machine cliente **Microsoft Windows 10** placée dans le réseau **LAN**.

### Vérification du réseau
La machine doit être capable de joindre l'interface web d'administration de pfSense. Nous commençons par vérifier les paramètres réseau de la machine avec la commande `ipconfig /all`.

> **Captures des tests Windows 10 :**

> ![Commande ipconfig](../images/ipconfigall_w10.png)
> ![Commande ping](../images/icmp_w10.png)
> ![Interface Web pfSense](../images/w10_internet1.png)

## 3. Connexion à l’interface web (LAN)

Une fois le réseau configuré, l'administration se fait via l'interface graphique depuis la machine cliente Windows 10.

### Accès à l'interface
Nous ouvrons un navigateur web et entrons l'adresse IP du LAN de pfSense (`10.0.0.1`).

![Accès Web pfSense](../images/weblogs_pfsense.png)

### Authentification
Pour la première connexion, nous utilisons les identifiants par défaut de pfSense :
* **Utilisateur :** `admin`
* **Mot de passe :** `pfsense`

![Identification pfSense](../images/basiclogs_pfsense.png)

### Assistant de configuration (Setup Wizard)
Après la connexion, pfSense lance un assistant pour finaliser les paramètres système.

![Assistant de configuration](../images/assist_config_pfsense.png)

Nous renseignons ensuite les informations d'identité du routeur ainsi que les serveurs DNS pour la résolution de noms.
* **Hostname :** (ex: pfSense)
* **Domain :** (ex: tssr.lab)
* **DNS :** 10.0.0.1 (DNS externes comme 1.1.1.1)

![Configuration DNS et Domaine](../images/domain_dns_pfsense.png)

## 4. Vérification et sécurisation finale

### Validation des interfaces WAN et LAN
L'adresse WAN ayant été configurée précédemment via le Shell, nous validons ici que les paramètres ont bien été repris par l'interface Web.

![Vérification Interface WAN](../images/wan_interface_pfsense.png)

Il en va de même pour l'interface LAN, qui servira de passerelle pour nos clients internes.

![Vérification Interface LAN](../images/lan_interface_pfsense.png)

### Sécurisation de l'accès
Par mesure de sécurité, nous procédons immédiatement à la modification du mot de passe de l'administrateur (`admin`) pour remplacer celui par défaut.

![Modification du mot de passe](../images/change_mdp_pfsense.png)

### État actuel de l'infrastructure
Voici le tableau de bord récapitulatif après cette première phase de configuration. 

> **Note :** On s'aperçoit à ce stade que si le WAN et le LAN sont opérationnels, la **DMZ** (visible sous le nom `OPT1`) reste encore à être configurée au niveau de l'adressage et de son nommage définitif.

![Liste des interfaces finales](../images/int_liste_pfsense.png)


## 5. Création et configuration de la zone DMZ

La DMZ (Demilitarized Zone) est essentielle pour isoler notre serveur Web du reste du réseau local (LAN). 

### Configuration de l'interface
Nous nous rendons dans le menu **Interfaces > OPT1**. Pour rendre l'administration plus claire, nous effectuons les modifications suivantes :
1. **Description :** Changement du nom par défaut `OPT1` en **DMZ**.
2. **Configuration IPv4 :** Nous vérifions que l'adresse IP de la passerelle (l'interface du pfSense côté DMZ) correspond bien à notre plan d'adressage (`172.16.0.1/24`).

![Configuration de l'interface DMZ](../images/config_dmz_pfsense.png)

Une fois ces modifications appliquées, la DMZ est officiellement active et prête à recevoir les règles de filtrage pour sécuriser les flux entrants et sortants.


---

## 6. Configuration des services réseau (LAN)

### Serveur DHCP
Afin d'automatiser l'adressage sur le réseau local, nous configurons pfSense comme serveur DHCP unique pour l'interface LAN.
* **Plage d'adresses (Scope) :** `10.0.0.50` à `10.0.0.100`
* **Passerelle :** `10.0.0.1`

![Configuration DHCP LAN](../images/dhcp_options_pfsense.png)

### Résolution de noms (DNS Resolver)
Nous configurons le **DNS Resolver** (Unbound) pour que pfSense assure la résolution de noms sur le LAN. 
1. Activation du service sur l'interface LAN.
2. Activation de l'option de résolution pour les clients DHCP (permet de résoudre les noms d'hôtes locaux).

![Activation DNS Resolver](../images/dns_lan_resolution_pfsense.png)
![Résolution des baux DHCP](../images/dns_resolution_dhcp_pfsense.png)


## 7. Tests de connectivité et validation (ICMP)

### Validation du bail DHCP
Après avoir désactivé le serveur DHCP de VMware Workstation (VMnet10) pour éviter les conflits, nous renouvelons l'adresse IP sur la machine cliente Windows 10. La commande `ipconfig /all` confirme que pfSense (`10.0.0.1`) a bien attribué une adresse dans la plage prévue.

![Vérification ipconfig](../images/new_ipconfigall_pfsense.png)

### Tests de communication
Pour valider l'ensemble de la pile réseau (L1 à L7), nous effectuons une série de pings :

1. **Ping de la passerelle :** Vérifie la connectivité locale avec pfSense.
   ![Ping Passerelle](../images/ping_passerelle_pfsense.png)
2. **Ping DNS Cloudflare (1.1.1.1) :** Vérifie l'accès à l'Internet extérieur (routage/NAT).
   ![Ping Cloudflare](../images/ping_dns_cloudflare_pfsense.png)
3. **Résolution Google.com :** Vérifie que le résolveur DNS de pfSense fonctionne correctement.
   ![Test DNS Google](../images/domain_google_pfsense.png)

   ## 2. Définition des règles du pare-feu

Pour garantir la sécurité de l'infrastructure, nous avons défini une politique de filtrage stricte sur pfSense. L'objectif est de compartimenter les flux afin qu'une compromission sur une zone (notamment la DMZ, exposée à l'extérieur) ne puisse pas impacter les autres zones sensibles comme le LAN.

Le tableau ci-dessous détaille la matrice de flux que nous avons implémentée :

![Définition des règles](../images/tableau_firewall_pfsense.png)

### Détails de la configuration par interface

#### 🛡️ Interface WAN
L'accès depuis l'extérieur est strictement limité aux services Web. Une règle de redirection de port (NAT) a été mise en place pour acheminer le trafic public (ports 80 et 443) vers l'adresse IP privée du serveur Debian situé dans la DMZ.

#### 💻 Interface LAN
Le réseau local est la zone de gestion. Les administrateurs peuvent accéder au serveur en DMZ via le protocole SSH pour la maintenance technique ou en HTTP pour vérifier le bon déploiement du site Web.

#### 🌐 Interface DMZ (Isolation)
C'est la règle la plus critique de notre architecture : **la DMZ ne peut en aucun cas initier de connexion vers le LAN**. En cas d'intrusion sur le serveur Web, l'attaquant est ainsi confiné dans cette zone isolée. Les seuls flux sortants autorisés sont dirigés vers l'Internet pour permettre la récupération des paquets de mise à jour du système.

## 3. Configuration de la passerelle WAN (VMnet8)

Pour permettre l'accès à Internet à l'ensemble de notre architecture, nous devons configurer la passerelle par défaut sur l'interface WAN. Cette interface est reliée au commutateur virtuel **VMnet8 (NAT)** de VMware, qui assure la liaison avec le réseau physique.

### Paramétrage du Gateway
Nous renseignons l'adresse IP de la passerelle NAT de VMware (généralement la `.2` du sous-réseau configuré) pour que pfSense puisse router le trafic vers l'extérieur.

![Configuration de la passerelle](../images/config_bond_pfsense.png)

Cette étape est indispensable pour que le serveur Debian puisse effectuer ses mises à jour et que les clients du LAN puissent naviguer sur le Web.

## 4. Configuration détaillée des flux et redirections

Cette section détaille la mise en place technique de la matrice de flux, allant de l'ouverture des services Web à l'isolation stricte de la DMZ.

### A. Accès Public (NAT / Port Forwarding)
Pour rendre notre futur serveur Web accessible depuis l'extérieur, nous avons configuré des redirections de ports sur l'interface WAN :
* **Port 80 (HTTP) :** Redirection vers le serveur Web en DMZ.
  ![Redirection Port 80](../images/wan_dmz80.png)
* **Port 443 (HTTPS) :** Redirection sécurisée vers le serveur Web.
  ![Redirection Port 443](../images/wan_dmz443.png)

### B. Administration depuis le LAN
Le réseau local doit pouvoir administrer et consulter le serveur en DMZ :
* **Flux HTTP (Port 80) :** Autorisation pour la vérification du site.
  ![Autorisation LAN vers DMZ 80](../images/lan80_dmz.png)
* **Flux SSH (Port 22) :** Autorisation pour l'administration distante du serveur Debian.
  ![Autorisation LAN vers DMZ 22](../images/lan22_dmz.png)

### C. Sécurisation et Isolation de la DMZ
La sécurité repose sur le cloisonnement. Nous avons appliqué les règles suivantes sur l'interface DMZ :
1. **Interdiction DMZ vers LAN :** Isolation totale pour éviter qu'un serveur compromis n'accède au réseau interne.
   ![Interdiction DMZ vers LAN](../images/deny_dmz_lan.png)
2. **Accès Internet (Mises à jour) :** Règle temporaire permettant au serveur de récupérer ses paquets.
   ![Règle Mise à jour DMZ](../images/rule_maj_dmz.png)
3. **Flux DNS :** Autorisation des requêtes DNS de la DMZ vers le WAN pour la résolution de noms.
   ![DNS DMZ vers WAN](../images/dns_dmz_wan.png)

### D. Synthèse des règles par interface
Voici l'état final des tables de filtrage après configuration :

* **Résultats Interface WAN :**
  ![Résultats WAN](../images/wan_rule_results.png)
* **Résultats Interface LAN :**
  ![Résultats LAN](../images/lan_rule_results.png)
* **Résultats Interface DMZ :**
  ![Résultats DMZ](../images/dmz_rule_results.png)

  ## 5. Validation et tests de connectivité (Recette)

Après la configuration des règles, une phase de tests rigoureux a été menée pour valider le bon cloisonnement des flux.

### A. Tests depuis le LAN (Zone de confiance)
Nous vérifions que le LAN peut administrer la DMZ comme prévu :
* **Test ICMP :** Ping réussi vers le futur serveur Web.
  ![Ping LAN vers DMZ](../images/ping_lan_dmz.png)
* **Accès Passerelle :** Ping de l'interface DMZ du pfSense.
  ![Ping Gateway DMZ](../images/gateway_dmz.png)
* **Test SSH :** Tentative de connexion sur le port 22 du serveur DMZ.
  ![SSH vers DMZ](../images/ssh_dmz.png)

### B. Tests depuis le WAN
Vérification de la visibilité du serveur depuis l'extérieur (via les règles de NAT) :
* **Test ICMP :** Ping vers l'adresse de la DMZ depuis le routeur.
  ![Ping WAN vers DMZ](../images/ping_pfsense_dmz.png)

### C. Tests depuis la DMZ (Zone isolée)
C'est ici que nous validons la sécurité. La DMZ doit être capable de sortir sur Internet mais ne doit jamais voir le LAN :
* **Isolation LAN :** Tentative de ping vers le LAN (Echec attendu).
  ![Test Isolation DMZ vers LAN](../images/rule_dmz_lan_ok.png)
* **Accès HTTP :** Test de récupération des dépôts (Mises à jour).
  ![Test Port 80 DMZ](../images/port80_ok.png)
* **Résolution DNS :** Vérification du fonctionnement du port 53.
  ![Test DNS DMZ](../images/port53_ok.png)

---

### ⚠️ Notes importantes de configuration

Lors de la création des règles dans pfSense, une attention particulière a été portée sur les points suivants :
* **Définition des cibles :** Utilisation de l'option **"Subnet"** ou de l'adresse IP exacte au lieu de "Interface Address" (qui ne cible que l'IP de la passerelle pfSense).
* **Ordre des règles :** Les tests ont été réalisés chronologiquement après chaque modification pour s'assurer qu'aucune règle globale ne masquait une règle spécifique (Lecture de haut en bas).
* **Pré-requis :** Le serveur Web a été provisionné en amont pour permettre ces tests, incluant une page `index.html` de test pour valider le flux HTTP avant la phase finale d'installation.

  


