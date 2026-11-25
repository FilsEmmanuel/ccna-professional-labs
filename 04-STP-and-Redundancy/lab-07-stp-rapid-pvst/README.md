# Lab 07 — STP et Rapid PVST+

**Days Jeremy's IT Lab :** 20–21 (Spanning Tree) et 22 (Rapid Spanning Tree)
**Durée estimée :** 2 h · **Niveau :** intermédiaire
**Statut : à réaliser.** Ce document est l'énoncé que je me suis fixé ; il sera remplacé par le compte rendu une fois le lab terminé dans Packet Tracer.

## Objectif

Comprendre comment le Spanning Tree supprime les boucles d'un réseau redondant, choisir le pont racine par VLAN pour répartir la charge, mesurer le gain de Rapid PVST+ et protéger les ports d'accès.

## Matériel (Packet Tracer)

| Équipement | Modèle | Rôle |
|---|---|---|
| SW1, SW2 | 2960-24TT | Commutateurs de distribution |
| SW3 | 2960-24TT | Commutateur d'accès |
| PC1, PC2 | PC-PT | PC1 dans le VLAN 10, PC2 dans le VLAN 20 |
| SW-PIRATE | 2960-24TT | Pour la panne uniquement |

## Câblage (triangle)

| De | Port | Vers | Port |
|---|---|---|---|
| SW1 | Gi0/1 | SW2 | Gi0/1 |
| SW1 | Gi0/2 | SW3 | Gi0/1 |
| SW2 | Gi0/2 | SW3 | Gi0/2 |
| PC1 | Fa0 | SW3 | Fa0/1 |
| PC2 | Fa0 | SW3 | Fa0/2 |

Tous les liens entre commutateurs sont des trunks autorisant les VLAN 10 et 20. Adresses : VLAN 10 = 192.168.10.0/24, VLAN 20 = 192.168.20.0/24. Ajoute PC3 (VLAN 10) sur SW1 Fa0/1 pour tester.

## Tâches

1. **Observation (PVST+ par défaut).** Pour le VLAN 10, identifie le pont racine et le rôle de chaque port (racine, désigné, bloqué). Justifie avec l'identifiant de pont (priorité + VLAN + MAC) et le coût.

| Commutateur | Port | Rôle | État | Pourquoi |
|---|---|---|---|---|

2. **Répartition de charge.** SW1 devient racine principale du VLAN 10 et secondaire du VLAN 20 ; SW2 l'inverse. Vérifie que le port bloqué n'est plus le même dans les deux VLAN.
3. **Mesure en PVST+.** Lance un ping continu PC1 → PC3, coupe le lien actif de SW3 vers la racine et compte les pings perdus.
4. **Rapid PVST+.** Passe les trois commutateurs en Rapid PVST+ et refais la même mesure. Compare.
5. **Protection des ports d'accès.** Active PortFast et BPDU Guard sur les ports des PC de SW3.

## Vérifications attendues

| Commande | Résultat attendu |
|---|---|
| `show spanning-tree vlan 10` | `This bridge is the root` sur SW1 ; rôles Root, Desg, Altn |
| `show spanning-tree vlan 20` | Racine sur SW2 ; port bloqué différent de celui du VLAN 10 |
| `show spanning-tree summary` | Mode `rapid-pvst` après l'étape 4 |
| Ping continu | Bien moins de pings perdus en Rapid PVST+ qu'en PVST+ |

## Panne à provoquer

Branche SW-PIRATE sur SW3 Fa0/1 à la place de PC1. Le port passe en `err-disabled`. Explique pourquoi, puis remets le port en service proprement.

## Questions d'entretien

1. Comment est élu le pont racine ?
2. Quels sont les états des ports en STP classique et combien de temps dure une convergence ?
3. Quelle différence entre un port Alternate et un port Backup en RSTP ?
4. Pourquoi n'active-t-on PortFast que vers des équipements terminaux ?

## Livrables (après réalisation dans Packet Tracer)

- `lab-07.pkt` : ta topologie finale, fonctionnelle.
- `configs/` : `show running-config` de chaque équipement, mots de passe remplacés par `<expurgé>`.
- `captures/` : captures des vérifications clés et de la panne corrigée.
- Sections ci-dessous remplies avec tes propres mots.

## Résultats

> À remplir : ce qui fonctionne, les sorties importantes, les tests réussis.

## Panne : diagnostic et correction

> À remplir : symptôme observé, commandes utilisées, cause trouvée, correction appliquée.

## Ce que j'ai appris

> À remplir : 3 à 5 points.
