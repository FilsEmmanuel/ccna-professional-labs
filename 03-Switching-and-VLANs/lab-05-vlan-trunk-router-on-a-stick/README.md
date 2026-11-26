# Lab 05 — VLAN, trunks 802.1Q et router-on-a-stick

**Days Jeremy's IT Lab :** 16–17 (VLAN, trunks, VLAN natif, router-on-a-stick)
**Durée estimée :** 2 h · **Niveau :** intermédiaire
**Statut : à réaliser.** Ce document est l'énoncé que je me suis fixé ; il sera remplacé par le compte rendu une fois le lab terminé dans Packet Tracer.

## Objectif

Segmenter un réseau en VLAN répartis sur deux switches, les relier par un trunk 802.1Q et les faire communiquer par un router avec des sous-interfaces.

## Matériel (Packet Tracer)

| Équipement | Modèle | Rôle |
|---|---|---|
| R1 | ISR4331 | Routage inter-VLAN (router-on-a-stick) |
| SW1, SW2 | 2960-24TT | Switches d'accès |
| PC1 à PC4 | PC-PT | Postes |

## Câblage

| De | Port | Vers | Port | Mode |
|---|---|---|---|---|
| SW1 | Gi0/2 | R1 | G0/0/0 | Trunk |
| SW1 | Gi0/1 | SW2 | Gi0/1 | Trunk |
| PC1 | Fa0 | SW1 | Fa0/1 | Access VLAN 10 |
| PC2 | Fa0 | SW1 | Fa0/2 | Access VLAN 20 |
| PC3 | Fa0 | SW2 | Fa0/1 | Access VLAN 10 |
| PC4 | Fa0 | SW2 | Fa0/2 | Access VLAN 20 |

## VLAN et adressage

| VLAN | Nom | Réseau | Gateway (R1) |
|---|---|---|---|
| 10 | ADMIN | 192.168.10.0/24 | G0/0/0.10 — 192.168.10.1 |
| 20 | ETUDIANTS | 192.168.20.0/24 | G0/0/0.20 — 192.168.20.1 |
| 30 | GESTION | 192.168.30.0/24 | G0/0/0.30 — 192.168.30.1 |
| 99 | NATIF | aucun | — |
| 999 | PARKING | aucun | — |

Gestion des switches : SW1 = 192.168.30.11, SW2 = 192.168.30.12 (interface VLAN 30). PC1 .10 et PC3 .11 dans le VLAN 10 ; PC2 .10 et PC4 .11 dans le VLAN 20.

## Tâches

1. Crée et nomme les VLAN sur SW1 et SW2.
2. Place chaque port de PC en mode access dans son VLAN.
3. Configure les trunks : mode trunk forcé, VLAN natif 99 des deux côtés, VLAN autorisés limités à 10, 20, 30 et 99.
4. Place tous les ports inutilisés dans le VLAN 999 et désactive-les.
5. Sur R1 : une sous-interface par VLAN avec encapsulation 802.1Q et l'adresse de gateway ; active l'interface physique.
6. Configure l'management IP address de chaque switch et sa gateway.
7. Teste : PC1 ↔ PC3 (même VLAN, sur deux switches), PC1 ↔ PC2 (inter-VLAN), PC1 → SW2 (gestion).

## Vérifications attendues

| Commande | Résultat attendu |
|---|---|
| `show vlan brief` | Chaque port dans le bon VLAN ; ports de trunk absents de la liste |
| `show interfaces trunk` | Mode `on`, encapsulation 802.1q, VLAN natif 99, VLAN autorisés 10,20,30,99 |
| `show ip interface brief` (R1) | Sous-interfaces `.10`, `.20`, `.30` en `up/up` |
| `tracert` PC1 → PC2 | First hop : 192.168.10.1 |

## Panne à provoquer

Sur le trunk SW1–SW2, retire le VLAN 20 de la liste des VLAN autorisés côté SW1. PC2 ↔ PC4 échoue mais PC1 ↔ PC3 fonctionne. Trouve la cause avec `show interfaces trunk` sur les deux switches.

## Questions d'entretien

1. Qu'est-ce que le VLAN natif ? Pourquoi éviter de laisser le VLAN 1 ?
2. Combien d'octets ajoute une étiquette 802.1Q, et où ?
3. Quelle différence entre un access port et un port trunk ?
4. Quelle est la limite principale du router-on-a-stick ?

## Livrables (après réalisation dans Packet Tracer)

- `lab-05.pkt` : ta topologie finale, fonctionnelle.
- `configs/` : `show running-config` de chaque équipement, mots de passe remplacés par `<expurgé>`.
- `captures/` : captures des vérifications clés et de la panne corrigée.
- Sections ci-dessous remplies avec tes propres mots.

## Résultats

> À remplir : ce qui fonctionne, les sorties importantes, les tests réussis.

## Panne : diagnostic et correction

> À remplir : symptôme observé, commandes utilisées, cause trouvée, correction appliquée.

## Ce que j'ai appris

> À remplir : 3 à 5 points.
