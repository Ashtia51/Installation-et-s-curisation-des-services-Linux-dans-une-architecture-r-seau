# I. Adressage IP et Contexte

Ce chapitre détaille la configuration réseau utilisée pour l'infrastructure sécurisée.

## Plan d'adressage

| Élément | Réseau / IP | Détails |
| :--- | :--- | :--- |
| **Réseau LAN** | `10.0.0.0/24` | Réseau interne |
| **Passerelle LAN** | `10.0.0.1/24` | IP Interface LAN Pfsense |
| **Plage DHCP LAN** | `10.0.0.50` > `10.0.0.100` | Attribution dynamique |
| **DNS** | `10.0.0.1` | Résolution de noms |
| **Réseau NAT** | `192.168.10.0/24` | Sortie WAN |
| **Passerelle NAT** | `192.168.10.2/24` | Sortie vers Internet |
| **Pfsense NAT** | `192.168.10.3/24` | IP Interface WAN |
| **Réseau DMZ** | `172.16.0.0/24` | Zone isolée |
| **Passerelle DMZ** | `172.16.0.1/24` | IP Interface DMZ Pfsense |
| **Serveur Web** | `172.16.0.3/24` | Debian (Hébergement) |

## Schémas du réseau

### Schéma Logique
Ce schéma représente les flux et l'organisation des sous-réseaux (LAN, DMZ, WAN).

![Schéma Logique](../images/schema_logique.png)

### Schéma Physique
Ce schéma détaille les équipements et les connexions physiques de l'infrastructure.

![Schéma Physique](../images/schema_physique.png)
