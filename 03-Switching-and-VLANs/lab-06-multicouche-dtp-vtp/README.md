# Lab 06 — Commutation multicouche, DTP et VTP

**Days Jeremy's IT Lab :** 18 (multilayer switching, SVI) et 19 (DTP et VTP)
**Durée estimée :** 2 h · **Niveau :** intermédiaire
**Statut : à réaliser.** Ce document est l'énoncé que je me suis fixé ; il sera remplacé par le compte rendu une fois le lab terminé dans Packet Tracer.

## Objectif

Remplacer le router-on-a-stick par un switch de cœur multicouche (SVI), comprendre la négociation DTP et la diffusion des VLAN par VTP, puis appliquer les bonnes pratiques.

## Matériel (Packet Tracer)

| Équipement | Modèle | Rôle |
|---|---|---|
| DSW1 | 3650-24PS | Cœur multicouche (ajoute le bloc d'alimentation AC dans l'onglet Physique) |
| ASW1, ASW2 | 2960-24TT | Accès |
| R1 | ISR4331 | Sortie vers Internet |
| PC1 à PC4 | PC-PT | Postes |

## Câblage

| De | Port | Vers | Port |
|---|---|---|---|
| ASW1 | Gi0/1 | DSW1 | Gi1/0/1 |
| ASW2 | Gi0/1 | DSW1 | Gi1/0/2 |
| DSW1 | Gi1/0/24 | R1 | G0/0/0 |
| PC1 / PC2 | Fa0 | ASW1 | Fa0/1 / Fa0/2 |
| PC3 / PC4 | Fa0 | ASW2 | Fa0/1 / Fa0/2 |

## VLAN et adressage

| VLAN | Nom | Réseau | SVI sur DSW1 |
|---|---|---|---|
| 10 | ADMIN | 192.168.10.0/24 | 192.168.10.1 |
| 20 | ETUDIANTS | 192.168.20.0/24 | 192.168.20.1 |
| 30 | GESTION | 192.168.30.0/24 | 192.168.30.1 |
| Lien DSW1 – R1 | — | 10.0.0.0/30 | DSW1 Gi1/0/24 = 10.0.0.2 (port routé), R1 = 10.0.0.1 |

PC1 et PC3 dans le VLAN 10, PC2 et PC4 dans le VLAN 20.

## Tâches

**Partie A — DTP**

1. Ne configure pas encore les trunks. Teste les combinaisons de modes DTP entre ASW1 et DSW1 (`dynamic auto`, `dynamic desirable`, `trunk`, `access`) et remplis le tableau ci-dessous.

| ASW1 \ DSW1 | dynamic auto | dynamic desirable | trunk | access |
|---|---|---|---|---|
| dynamic auto | | | | |
| dynamic desirable | | | | |
| trunk | | | | |

2. Applique la bonne pratique : trunk forcé et négociation DTP désactivée.

**Partie B — VTP**

3. DSW1 en mode serveur, domaine `CAMPUS`, mot de passe VTP ; ASW1 et ASW2 en mode client avec le même domaine et le même mot de passe.
4. Crée les VLAN 10, 20 et 30 uniquement sur DSW1 et vérifie qu'ils apparaissent sur les switches d'accès.
5. Relève le numéro de révision VTP avant et après la création d'un VLAN.

**Partie C — Routage multicouche**

6. Sur DSW1 : active le routage IP, crée les SVI, configure Gi1/0/24 en port routé et ajoute une default route vers R1.
7. Sur R1 : une summary route `192.168.0.0/16` vers DSW1.
8. Teste PC1 ↔ PC2, PC1 ↔ PC4 et PC1 → 10.0.0.1.

## Vérifications attendues

| Commande | Résultat attendu |
|---|---|
| `show interfaces trunk` | Trunks en mode `on`, négociation désactivée |
| `show vtp status` | Même domaine, rôles serveur et client, même numéro de révision |
| `show ip route` (DSW1) | Réseaux des VLAN en `C`, default route `S*` |
| `show interfaces gi1/0/24 switchport` | `Switchport: Disabled` |

## Panne à provoquer

Sur ASW2, change le nom de domaine VTP en `CAMPUS2`. Supprime puis recrée le VLAN 20 sur DSW1 : ASW2 ne reçoit plus les VLAN et PC4 perd la connectivité. Diagnostique avec `show vtp status` et `show vlan brief`.

## Questions d'entretien

1. Quels avantages a une SVI par rapport au router-on-a-stick ?
2. Pourquoi le numéro de révision VTP peut-il effacer les VLAN d'un réseau ?
3. Pourquoi désactiver DTP sur les ports ?
4. Quelle différence entre une SVI et un port routé ?

## Livrables (après réalisation dans Packet Tracer)

- `lab-06.pkt` : ta topologie finale, fonctionnelle.
- `configs/` : `show running-config` de chaque équipement, mots de passe remplacés par `<expurgé>`.
- `captures/` : captures des vérifications clés et de la panne corrigée.
- Sections ci-dessous remplies avec tes propres mots.

## Résultats

> À remplir : ce qui fonctionne, les sorties importantes, les tests réussis.

## Panne : diagnostic et correction

> À remplir : symptôme observé, commandes utilisées, cause trouvée, correction appliquée.

## Ce que j'ai appris

> À remplir : 3 à 5 points.
