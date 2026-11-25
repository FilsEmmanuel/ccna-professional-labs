# Lab 08 — EtherChannel LACP, PAgP et couche 3

**Days Jeremy's IT Lab :** 23 (EtherChannel)
**Durée estimée :** 1 h 30 · **Niveau :** intermédiaire
**Statut : à réaliser.** Ce document est l'énoncé que je me suis fixé ; il sera remplacé par le compte rendu une fois le lab terminé dans Packet Tracer.

## Objectif

Agréger plusieurs liens physiques en un seul lien logique, plus rapide et redondant, avec LACP, PAgP et en couche 3 entre deux commutateurs de cœur.

## Matériel (Packet Tracer)

| Équipement | Modèle | Rôle |
|---|---|---|
| DSW1, DSW2 | 3650-24PS | Cœur (ajoute le bloc d'alimentation) |
| ASW1, ASW2 | 2960-24TT | Accès |
| PC1, PC2 | PC-PT | Postes du VLAN 10 |

## Câblage

| Agrégat | Membres | Protocole | Type |
|---|---|---|---|
| Po1 : ASW1 – DSW1 | ASW1 Gi0/1-2 ↔ DSW1 Gi1/0/1-2 | LACP | Couche 2, trunk |
| Po2 : ASW2 – DSW2 | ASW2 Gi0/1-2 ↔ DSW2 Gi1/0/1-2 | PAgP | Couche 2, trunk |
| Po3 : DSW1 – DSW2 | DSW1 Gi1/0/23-24 ↔ DSW2 Gi1/0/23-24 | LACP | Couche 3, 10.0.12.0/30 |

PC1 sur ASW1 Fa0/1, PC2 sur ASW2 Fa0/1. VLAN 10 : SVI 192.168.10.1 sur DSW1 et VLAN 20 : SVI 192.168.20.1 sur DSW2 (place PC2 dans le VLAN 20). Routes statiques entre DSW1 et DSW2 via Po3.

## Tâches

1. Configure Po1 en LACP (actif d'un côté, passif de l'autre) puis passe-le en trunk.
2. Configure Po2 en PAgP (desirable / auto) puis passe-le en trunk.
3. Configure Po3 en couche 3 : ports non commutés, adresse IP sur l'interface Port-channel.
4. Choisis la méthode de répartition de charge `src-dst-ip` sur les commutateurs qui la proposent.
5. Ajoute les SVI, les routes et teste PC1 ↔ PC2.
6. **Redondance.** Ping continu PC1 → PC2, coupe un membre de chaque agrégat, puis observe.

## Vérifications attendues

| Commande | Résultat attendu |
|---|---|
| `show etherchannel summary` | Po1 et Po2 en `(SU)`, Po3 en `(RU)`, membres en `(P)` |
| `show etherchannel port-channel` | Protocole LACP ou PAgP selon l'agrégat |
| `show interfaces trunk` | Po1 et Po2 apparaissent comme trunks, pas les ports physiques |
| Ping pendant la coupure d'un membre | Aucune perte ou presque |

## Panne à provoquer

Passe les deux côtés de Po1 en LACP passif. L'agrégat ne se forme plus. Explique pourquoi, puis corrige. Ensuite, change la vitesse d'un seul membre de Po2 et observe son état (`s`, suspendu).

## Questions d'entretien

1. Quelles combinaisons de modes LACP et PAgP forment un agrégat ?
2. Pourquoi les membres doivent-ils avoir exactement la même configuration ?
3. Comment le trafic est-il réparti entre les liens ?
4. Pourquoi le STP voit-il un agrégat comme un seul lien ?

## Livrables (après réalisation dans Packet Tracer)

- `lab-08.pkt` : ta topologie finale, fonctionnelle.
- `configs/` : `show running-config` de chaque équipement, mots de passe remplacés par `<expurgé>`.
- `captures/` : captures des vérifications clés et de la panne corrigée.
- Sections ci-dessous remplies avec tes propres mots.

## Résultats

> À remplir : ce qui fonctionne, les sorties importantes, les tests réussis.

## Panne : diagnostic et correction

> À remplir : symptôme observé, commandes utilisées, cause trouvée, correction appliquée.

## Ce que j'ai appris

> À remplir : 3 à 5 points.
