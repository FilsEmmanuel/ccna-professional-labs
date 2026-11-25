# Lab 09 — Routage dynamique : RIPv2, EIGRP puis OSPF

**Days Jeremy's IT Lab :** 24 (routage dynamique), 25 (RIP et EIGRP), 26–28 (OSPF)
**Durée estimée :** 3 h · **Niveau :** intermédiaire à avancé
**Statut : à réaliser.** Ce document est l'énoncé que je me suis fixé ; il sera remplacé par le compte rendu une fois le lab terminé dans Packet Tracer.

## Objectif

Faire fonctionner la même topologie avec trois protocoles successifs, comparer leurs distances administratives et leurs métriques, puis optimiser OSPF.

## Matériel (Packet Tracer)

| Équipement | Modèle | Rôle |
|---|---|---|
| R1 | ISR4331 | Routeur de bordure (vers ISP) |
| R2, R3, R4 | ISR4331 | Routeurs internes |
| ISP | ISR4331 | Internet simulé (Loopback 0 = 198.51.100.1/32) |
| SW2, SW3, SW4 + 1 PC chacun | 2960-24TT / PC-PT | LAN des routeurs internes |

## Câblage (carré)

| Lien | Interfaces | Réseau |
|---|---|---|
| R1 – R2 | G0/0/0 ↔ G0/0/0 | 10.0.12.0/30 |
| R1 – R3 | G0/0/1 ↔ G0/0/0 | 10.0.13.0/30 |
| R2 – R4 | G0/0/1 ↔ G0/0/0 | 10.0.24.0/30 |
| R3 – R4 | G0/0/1 ↔ G0/0/1 | 10.0.34.0/30 |
| R1 – ISP | G0/0/2 ↔ G0/0/0 | 203.0.113.0/30 (ISP .1, R1 .2) |
| LAN R2, R3, R4 | G0/0/2 | 10.2.0.0/24, 10.3.0.0/24, 10.4.0.0/24 (routeur .1) |
| LAN R1 | Loopback 1 | 10.1.0.1/24 |

## Tâches

**Phase 1 — RIPv2**

1. Active RIP version 2 sur tous les routeurs internes, sans résumé automatique, interfaces LAN en passif.
2. Vérifie que chaque routeur connaît tous les réseaux et relève la distance administrative et la métrique (nombre de sauts).

**Phase 2 — EIGRP (laisse RIP actif)**

3. Active EIGRP AS 100 avec des masques génériques précis, sans résumé automatique, interfaces LAN en passif.
4. Observe que les routes RIP (`R`, 120) sont remplacées par des routes EIGRP (`D`, 90). Sur R1, relève dans la table de topologie EIGRP les successeurs vers 10.4.0.0/24 : explique pourquoi il y en a deux (partage de charge à coût égal), puis change la bande passante d'un lien pour faire apparaître un successeur et un successeur possible.
5. Retire RIP.

**Phase 3 — OSPF (laisse EIGRP actif)**

6. Active OSPF processus 1, zone 0, avec un router-id fixe par routeur (1.1.1.1, 2.2.2.2…), `passive-interface default` puis réactivation sur les liens entre routeurs.
7. Observe : les routes EIGRP (90) restent préférées à OSPF (110). Retire EIGRP et vérifie que les routes `O` apparaissent.
8. Passe les liens en type point à point et règle une bande passante de référence identique sur tous les routeurs.
9. Sur R1 : route par défaut vers ISP et annonce de cette route dans OSPF. ISP a une route résumée `10.0.0.0/8` vers R1.
10. Modifie le coût d'un lien pour forcer le trafic R1 → LAN R4 à passer par R3 ; vérifie avec `traceroute`.

## Vérifications attendues

| Commande | Résultat attendu |
|---|---|
| `show ip protocols` | Protocole actif, réseaux annoncés, interfaces passives |
| `show ip eigrp neighbors` / `show ip eigrp topology` | Voisins établis ; deux successeurs, puis successeur et successeur possible |
| `show ip ospf neighbor` | Voisins en `FULL` (sans DR/BDR après le passage en point à point) |
| `show ip route ospf` | Routes `O` vers chaque LAN et `O*E2` par défaut sur R2, R3, R4 |
| `traceroute` R2 → 198.51.100.1 | Réussi via R1 |

## Panne à provoquer

Sur R4, change le timer hello OSPF de l'interface vers R3. Le voisinage disparaît. Diagnostique avec `show ip ospf interface` et `show ip ospf neighbor`.

## Questions d'entretien

1. Distances administratives de RIP, EIGRP, OSPF et d'une route statique ?
2. Quels sont les états d'un voisinage OSPF jusqu'à `FULL` ?
3. Comment est élu le routeur désigné (DR) ?
4. Comment OSPF calcule-t-il le coût d'une interface ?

## Livrables (après réalisation dans Packet Tracer)

- `lab-09.pkt` : ta topologie finale, fonctionnelle.
- `configs/` : `show running-config` de chaque équipement, mots de passe remplacés par `<expurgé>`.
- `captures/` : captures des vérifications clés et de la panne corrigée.
- Sections ci-dessous remplies avec tes propres mots.

## Résultats

> À remplir : ce qui fonctionne, les sorties importantes, les tests réussis.

## Panne : diagnostic et correction

> À remplir : symptôme observé, commandes utilisées, cause trouvée, correction appliquée.

## Ce que j'ai appris

> À remplir : 3 à 5 points.
