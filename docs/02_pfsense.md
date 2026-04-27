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







