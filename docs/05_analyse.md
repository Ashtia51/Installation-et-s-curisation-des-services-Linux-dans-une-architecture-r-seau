
# VI. Conclusion et analyse de sécurité

Ce projet nous a permis de mettre en place une infrastructure réseau complète, robuste et supervisée. L'utilité de chaque manipulation réside dans la superposition des couches de sécurité (Defense in Depth).

## Synthèse de l'infrastructure
* **Segmentation Réseau :** Grâce à **pfSense**, nous avons compartimenté les flux entre le WAN (accès Internet), le LAN (administration) et la DMZ (hébergement). L'isolation de la DMZ garantit que même en cas de compromission du serveur Web, le réseau local reste protégé.
* **Hardening Système :** Le passage du service SSH sur le **port 5091** et l'exigence d'une **clé RSA 4096 bits** éliminent les vecteurs d'attaque par brute-force les plus courants.
* **Sécurité Applicative :** L'installation de **FirewallD** sur le serveur Debian agit comme une seconde barrière, tandis que **Fail2Ban** bannit dynamiquement les comportements suspects.
* **Supervision :** L'intégration de **Netdata** offre une visibilité critique sur l'état de santé et les performances du serveur depuis le LAN.

## Perspectives d'amélioration
Bien que l'infrastructure actuelle soit hautement sécurisée, la sécurité est un processus continu. Pour augmenter encore le niveau de protection, nous pourrions envisager :
* **Antivirus :** Installation de **ClamAV** pour scanner les fichiers sur le serveur.
* **Anonymat et Accès :** Mise en place d'un **VPN** pour l'administration distante et d'un **Proxy/Reverse-Proxy** pour mieux gérer les requêtes entrantes.
* **Certificats :** Implémentation de SSL/TLS (Let's Encrypt) pour passer l'intégralité du trafic Web en HTTPS.

Ce dossier démontre qu'avec des outils Open-Source et une configuration rigoureuse, il est possible de bâtir une architecture professionnelle répondant aux exigences actuelles de cybersécurité.
