# Lab 03 — Routage statique, route par défaut et route statique flottante

**Days Jeremy's IT Lab :** 11 (routage statique) et 24 (routage dynamique : distance administrative, routes flottantes)
**Durée estimée :** 2 h · **Niveau :** intermédiaire
**Statut : à réaliser.** Ce document est l'énoncé que je me suis fixé ; il sera remplacé par le compte rendu une fois le lab terminé dans Packet Tracer.

## Objectif

Relier deux sites et un accès Internet simulé avec des routes statiques, puis prévoir un chemin de secours automatique grâce à des routes statiques flottantes.

## Matériel (Packet Tracer)

| Équipement | Modèle | Rôle |
|---|---|---|
| R1 | ISR4331 | Routeur du site A |
| R2 | ISR4331 | Routeur central, vers Internet |
| R3 | ISR4331 | Routeur du site C |
| ISP | ISR4331 | Fournisseur d'accès simulé |
| SW1, SW3 | 2960-24TT | Commutateurs des LAN |
| PC1, PC3 | PC-PT | Postes |

## Câblage

| De | Port | Vers | Port |
|---|---|---|---|
| R1 | G0/0/0 | R2 | G0/0/0 |
| R1 | G0/0/1 | R3 | G0/0/0 |
| R2 | G0/0/1 | R3 | G0/0/1 |
| R2 | G0/0/2 | ISP | G0/0/0 |
| R1 | G0/0/2 | SW1 | Gi0/1 |
| R3 | G0/0/2 | SW3 | Gi0/1 |
| PC1 | Fa0 | SW1 | Fa0/1 |
| PC3 | Fa0 | SW3 | Fa0/1 |

## Plan d'adressage

| Lien ou réseau | Réseau | Adresses |
|---|---|---|
| LAN A (R1) | 10.1.1.0/24 | R1 .1, PC1 .10 |
| LAN C (R3) | 10.1.3.0/24 | R3 .1, PC3 .10 |
| R1 – R2 | 10.0.12.0/30 | R1 .1, R2 .2 |
| R1 – R3 | 10.0.13.0/30 | R1 .1, R3 .2 |
| R2 – R3 | 10.0.23.0/30 | R2 .1, R3 .2 |
| R2 – ISP | 203.0.113.0/30 | ISP .1, R2 .2 |
| « Internet » | Loopback 0 de ISP | 198.51.100.1/32 |

## Tâches

1. Configure les interfaces et vérifie la connectivité de chaque lien direct.
2. **Routes vers les LAN.** Sur R1, une route vers le LAN C par le lien direct R1–R3. Sur R3, une route vers le LAN A par le même lien. Sur R2, des routes vers les deux LAN.
3. **Routes par défaut.** R1 et R3 envoient tout le reste vers R2 ; R2 envoie tout le reste vers ISP.
4. **Route résumée.** Sur ISP, une seule route `10.0.0.0/8` vers R2 pour revenir vers tout le réseau de l'entreprise.
5. **Routes flottantes.** Sur R1, une route de secours vers le LAN C via R2 (distance administrative 10). Sur R3, une route de secours vers le LAN A via R2. Sur R1, une route par défaut de secours via R3.
6. Teste un ping et un `traceroute` de PC1 vers PC3 et vers 198.51.100.1.
7. **Bascule.** Lance un ping continu PC1 → PC3 (`ping -t`), désactive le lien R1–R3, puis observe la table de routage et le nouveau chemin. Réactive le lien.

**À comprendre :** route avec adresse de saut suivant, avec interface de sortie, ou les deux ; distance administrative ; route hôte /32.

## Vérifications attendues

| Commande | Résultat attendu |
|---|---|
| `show ip route static` (R1) | Route vers 10.1.3.0/24 avec `[1/0]`, route par défaut `S*` |
| Même commande, lien R1–R3 coupé | La route flottante `[10/0]` via R2 apparaît |
| `traceroute` PC1 → PC3 | Passe par R3 directement, puis par R2 pendant la panne |
| `ping 198.51.100.1` depuis PC1 | Réussi grâce aux routes par défaut et à la route résumée |

## Panne à provoquer

Sur R3, remplace le saut suivant de la route vers le LAN A par une adresse inexistante (10.0.13.5). Le ping PC1 → PC3 échoue alors que les deux routeurs se joignent. Trouve la cause avec `traceroute` depuis chaque PC et `show ip route`.

## Questions d'entretien

1. Pourquoi une route flottante n'apparaît-elle pas dans la table tant que la route principale est active ?
2. Quelle est la distance administrative d'une route statique ? D'une route directement connectée ?
3. Dans quel cas une route flottante ne bascule-t-elle pas, alors que le chemin principal est en panne ?
4. À quoi sert une route résumée ?

## Livrables (après réalisation dans Packet Tracer)

- `lab-03.pkt` : ta topologie finale, fonctionnelle.
- `configs/` : `show running-config` de chaque équipement, mots de passe remplacés par `<expurgé>`.
- `captures/` : captures des vérifications clés et de la panne corrigée.
- Sections ci-dessous remplies avec tes propres mots.

## Résultats

> À remplir : ce qui fonctionne, les sorties importantes, les tests réussis.

## Panne : diagnostic et correction

> À remplir : symptôme observé, commandes utilisées, cause trouvée, correction appliquée.

## Ce que j'ai appris

> À remplir : 3 à 5 points.
