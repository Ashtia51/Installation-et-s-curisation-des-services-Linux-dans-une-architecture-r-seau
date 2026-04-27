# II. Installation et configuration de pfSense

## 1. Configuration des interfaces
L'installation de pfSense suit le processus basique. Une fois l'installation terminée, nous arrivons sur le menu principal permettant la configuration des interfaces.

> **Captures de l'installation :**
> ![Installation pfSense 1](../images/install_pfsense.png)
> ![Installation pfSense 2](../images/config_pfsense.png)

### Assignation des interfaces et IP
Pour configurer notre architecture, nous suivons les étapes suivantes :
1. Nous appuyons sur **« 1 »** pour assigner les interfaces physiques.
2. Nous appuyons sur **« 2 »** pour configurer les adresses IP du **WAN**, du **LAN**, et de la **DMZ** (qui garde le nom par défaut `OPT1` pour le moment).

Voici le résultat de la configuration :

> **Captures de configuration IP :**
> ![Assignation interfaces](../images/assign_int_pfsense.png)
> ![Configuration WAN](../images/assign_int_pfsense_WAN.png)
> ![Configuration LAN](../images/assign_int_pfsense_LAN.png)
> ![Configuration DMZ/OPT1](../images/assign_int_pfsense_DMZ.png)
> ![Résumé des interfaces](../images/assign_int_pfsense_final.png)


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



