# TP4

## Table d'adressage

### Vlans et Réseaux

|  Vlans  | Réseau Serveurs | Réseau Clients | Réseau Admin |
|:-------:|:---------------:|:--------------:|:------------:|
| Vlan 10 |   10.6.10.0/28  |        -       |       -      |
| Vlan 20 |        -        |  10.6.20.0/27  |       -      |
| Vlan 30 |        -        |        -       | 10.6.30.0/30 |

### IPs

| Machines |   Vlan 10  |   Vlan 20  |  Vlan 30  |
|:--------:|:----------:|:----------:|:---------:|
|  Pfsense | 10.6.10.14 | 10.6.20.30 | 10.6.30.1 |
|  Server1 |  10.6.10.1 |      -     |     -     |
|  Server2 |  10.6.10.2 |      -     |     -     |
|  Client1 |      -     |  10.6.20.1 |     -     |
|  Client2 |      -     |  10.6.20.2 |     -     |
|   Admin  |      -     |      -     | 10.6.30.2 |

## Projet initial

> L'Infra

* Un Pfsense reliant la nat et le réseau lan.

* 3 Switch 1 principal et 1 spécifique à chaque Vlan

* 5 machines CentOS 7:
  * 2 Serveurs
  * 2 Clients
  * 1 Admin (Avec une interface KDE)

> Sécurité des flux

* Mise en place un Pfsense qui sert de pare-feux et de routeur.
* L'administration se fais avec une interface Web : 
  * Les règles de pare-feux: Accéder au serveur Web héberger sur le Serveur 1 depuis l'extéruieur
  * Le routage, accès réseaux
* Configuration les Vlan via l'interface console de Pfsense.

* La DMZ permet d'accéder au serveur Web héberger sur le Serveur 1 depuis l'extérieur.

* Il y a un Vlan pour les clients, un pour les serveurs et un pour l'admin.
  
* Désactivation du SSH sur Pfsense, un mot de passe sécurisé sur Pfsense et les switchs et une 
  * bidentification uniquement par clef.

> Services réseau

* Un serveur DNS pour les noms des différents équipements du réseau et sécurisé avec DNSSEC:
  * Le Serveur DNS est sur le CentOS
  * Le DNSSEC permet de sécuriser les requêtes DNS de bout en bout

* NGINX avec son pare-feux applicatif naxsi
  * J'ai choisie NGINX car j'ai remarqué qu'il est de plus en plus utilisé pour ça fiabilité et surtout parcequ'il est deux fois plus rapide :)

> Monitoring/Métrologie

* Mise en place de nTopng pour l'analyse complète du réseau et de ces équipements

Pfsense + Gns3 = pas bien (je me suis plus servie de EVE-ng);
