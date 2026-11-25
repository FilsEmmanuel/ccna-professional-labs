# Lab 10 — Projet de synthèse : réseau de campus multisite

**Days Jeremy's IT Lab :** 1 à 30, dont 29 (redondance de passerelle, FHRP)
**Durée estimée :** 6 à 8 h, en plusieurs séances · **Niveau :** avancé
**Statut : à réaliser.** Ce document est l'énoncé que je me suis fixé ; il sera remplacé par le compte rendu une fois le lab terminé dans Packet Tracer.

## Objectif

Concevoir, construire, tester et documenter le réseau d'une organisation fictive à deux sites : un campus principal redondant et une annexe, reliés par un lien WAN avec chemin de secours. Ce lab réunit toutes les compétences des labs 01 à 09 et sert de projet phare du portfolio.

## Architecture

**Campus principal (modèle à deux niveaux : cœur/distribution et accès)**

| Équipement | Modèle | Rôle |
|---|---|---|
| DSW1, DSW2 | 3650-24PS | Distribution/cœur, routage inter-VLAN, HSRP |
| ASW1, ASW2 | 2960-24TT | Accès, chacun relié aux deux DSW |
| R-EDGE | ISR4331 + module NIM-2T | Bordure : vers l'annexe (série) et vers ISP |
| SRV1 | Server-PT | Serveur DNS et web interne (VLAN SERVEURS) |
| PC | PC-PT | Au moins un par VLAN |

**Annexe**

| Équipement | Modèle | Rôle |
|---|---|---|
| R-ANNEXE | ISR4331 + module NIM-2T | Routeur de l'annexe, router-on-a-stick |
| ASW3 | 2960-24TT | Accès |
| PC | PC-PT | Un par VLAN |

**Internet simulé :** ISP (ISR4331), Loopback 0 = 198.51.100.1/32.

**Préparation Packet Tracer :** un ISR4331 n'a que 3 ports GigabitEthernet. Sur R-EDGE et R-ANNEXE, éteins le routeur dans l'onglet Physique, ajoute un module **NIM-2T** (2 ports série), rallume-le, puis relie les deux routeurs par deux câbles série DCE. Sur les 3650, ajoute le bloc d'alimentation AC.

## Liens principaux

| Lien | Type |
|---|---|
| DSW1 – DSW2 | EtherChannel LACP couche 2 (2 liens), trunk |
| ASW1 et ASW2 – DSW1 et DSW2 | Trunks (un lien vers chaque DSW) |
| DSW1 – R-EDGE et DSW2 – R-EDGE | Ports routés, /30, OSPF |
| R-EDGE – R-ANNEXE | Lien WAN principal série (S0/1/0), /30, OSPF |
| R-EDGE – R-ANNEXE | Second lien série de secours (S0/1/1), /30, route statique flottante |
| R-EDGE – ISP | /30 en 203.0.113.0/30 |

## Cahier des charges d'adressage

Bloc attribué : **10.10.0.0/16**. Calcule le plan avec ton script du lab 04, puis vérifie-le à la main.

| Site | VLAN | Nom | Hôtes |
|---|---|---|---|
| Campus | 10 | ADMINISTRATION | 100 |
| Campus | 20 | ETUDIANTS | 400 |
| Campus | 30 | GESTION-RESEAU | 20 |
| Campus | 40 | SERVEURS | 10 |
| Annexe | 110 | ANNEXE-ADMIN | 50 |
| Annexe | 120 | ANNEXE-ETUDIANTS | 100 |
| Liens | — | 4 liens point à point internes (2 vers R-EDGE, 2 WAN) | 2 chacun |

Dans chaque VLAN du campus : .1 = adresse virtuelle HSRP (passerelle des PC), .2 = DSW1, .3 = DSW2.

## Tâches, par phase

**Phase 1 — Conception (avant Packet Tracer)**

1. Schéma logique (draw.io ou équivalent) exporté en `schema.png`.
2. Plan d'adressage complet, généré par ton script et vérifié.

**Phase 2 — Commutation du campus**

3. Configuration de base sécurisée de tous les équipements (lab 01).
4. VLAN, ports d'accès, trunks avec VLAN natif dédié et VLAN autorisés limités ; ports inutilisés en VLAN parking et désactivés (lab 05). VTP en mode transparent.
5. EtherChannel LACP entre DSW1 et DSW2 (lab 08).
6. Rapid PVST+ : DSW1 racine des VLAN 10 et 30, DSW2 racine des VLAN 20 et 40 ; PortFast et BPDU Guard sur les ports d'accès (lab 07).

**Phase 3 — Routage et redondance de passerelle**

7. SVI sur DSW1 et DSW2, routage IP activé (lab 06).
8. HSRP version 2 sur chaque VLAN : DSW1 actif pour les VLAN 10 et 30, DSW2 actif pour les VLAN 20 et 40 (priorité 110 et préemption), aligné sur les racines STP.
9. OSPF zone 0 entre DSW1, DSW2, R-EDGE et R-ANNEXE ; interfaces LAN en passif ; router-id fixes (lab 09).
10. Annexe : router-on-a-stick pour les VLAN 110 et 120 sur R-ANNEXE et ASW3, réseaux annoncés dans OSPF (lab 05).
11. Route par défaut de R-EDGE vers ISP, annoncée dans OSPF ; route résumée 10.10.0.0/16 sur ISP (lab 03).
12. Second lien WAN : routes statiques flottantes avec une distance administrative supérieure à celle d'OSPF (lab 03).
13. SRV1 : DNS (`intranet.campus.local`) et HTTP (lab 02).

**Phase 4 — Tests de recette**

| # | Test | Résultat attendu | Résultat obtenu |
|---|---|---|---|
| T1 | PC ADMIN campus → PC ETUDIANTS annexe | Réussi | |
| T2 | Tout PC → `http://intranet.campus.local` | Page affichée | |
| T3 | Tout PC → 198.51.100.1 | Réussi | |
| T4 | Ping continu, puis arrêt de DSW1 | Bascule HSRP sur DSW2, quelques pertes au plus | |
| T5 | Ping continu, puis coupure d'un membre de l'EtherChannel | Aucune perte ou presque | |
| T6 | Coupure du lien WAN principal | La route flottante prend le relais | |
| T7 | Branchement d'un commutateur sur un port d'accès | Port en `err-disabled` | |

## Vérifications clés

`show standby brief` · `show spanning-tree root` · `show etherchannel summary` · `show ip ospf neighbor` · `show ip route` · `show interfaces trunk` · `traceroute`

## Pannes à provoquer (une à la fois)

1. Sur DSW2, place par erreur l'interface vers R-EDGE en passif OSPF.
2. Sur DSW2, donne au VLAN 10 une priorité HSRP supérieure à celle de DSW1 : le trafic suit un chemin non optimal par rapport au STP.
3. Sur ASW3, retire le VLAN 120 du trunk vers R-ANNEXE.

Pour chacune : symptôme, commandes, cause, correction.

## Documentation finale (README de ce dossier)

Contexte et objectifs, schéma, plan d'adressage, choix de conception justifiés (pourquoi HSRP aligné sur STP, pourquoi VTP transparent, pourquoi des routes flottantes), configurations expurgées, tableau de recette rempli, pannes et corrections, limites et améliorations possibles (par exemple : DHCP, ACL, SSH, NAT, à ajouter après les Days suivants).

## Questions d'entretien

1. Pourquoi aligner le routeur HSRP actif sur la racine STP de chaque VLAN ?
2. Que se passe-t-il pour les PC quand DSW1 tombe en panne ?
3. Comment OSPF et la route flottante cohabitent-ils sur le lien WAN ?
4. Quelles améliorations de sécurité ajouterais-tu à ce réseau ?

## Livrables (après réalisation dans Packet Tracer)

- `lab-10.pkt` : ta topologie finale, fonctionnelle.
- `configs/` : `show running-config` de chaque équipement, mots de passe remplacés par `<expurgé>`.
- `captures/` : captures des vérifications clés et de la panne corrigée.
- Sections ci-dessous remplies avec tes propres mots.

## Résultats

> À remplir : ce qui fonctionne, les sorties importantes, les tests réussis.

## Panne : diagnostic et correction

> À remplir : symptôme observé, commandes utilisées, cause trouvée, correction appliquée.

## Ce que j'ai appris

> À remplir : 3 à 5 points.
