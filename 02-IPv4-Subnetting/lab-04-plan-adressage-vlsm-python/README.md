# Lab 04 — Plan d'adressage VLSM et script Python

**Days Jeremy's IT Lab :** 7–8 (adressage IPv4) et 13–15 (subnets et VLSM)
**Durée estimée :** 3 h · **Niveau :** intermédiaire
**Statut : à réaliser.** Ce document est l'énoncé que je me suis fixé ; il sera remplacé par le compte rendu une fois le lab terminé dans Packet Tracer.

## Objectif

Concevoir à la main le plan d'adressage d'une organisation avec VLSM, l'appliquer dans Packet Tracer, puis écrire un script Python qui calcule le même plan pour vérifier tes calculs.

## Cahier des charges

Bloc attribué : **172.16.0.0/23**. Besoins (nombre d'hôtes utilisables) :

| Réseau | Hôtes nécessaires |
|---|---|
| WIFI-INVITES | 250 |
| ADMINISTRATION | 120 |
| PEDAGOGIE | 60 |
| LABO-INFO | 28 |
| SERVEURS | 12 |
| Liens point à point R1–R2, R1–R3, R2–R3 | 2 chacun |

## Partie 1 — Calcul à la main

1. Classe les besoins du plus grand au plus petit.
2. Pour chacun, choisis le plus petit masque qui suffit et attribue les réseaux à la suite, sans chevauchement.
3. Remplis le tableau : réseau, masque (décimal et CIDR), première adresse, dernière adresse, diffusion, gateway (première adresse utilisable).

**Point de contrôle :** si ton plan est correct, la dernière adresse attribuée (diffusion du dernier lien point à point) est 172.16.1.251.

## Partie 2 — Mise en œuvre dans Packet Tracer

| Équipement | Modèle | Rôle |
|---|---|---|
| R1 | ISR4331 | ADMINISTRATION, PEDAGOGIE, lien vers R2 |
| R2 | ISR4331 | LABO-INFO, SERVEURS, lien vers R1 |
| 4 switches | 2960-24TT | Un par réseau |
| 4 PC (ou 3 PC + 1 serveur) | PC-PT / Server-PT | Un par réseau |

1. Applique ton plan sur R1, R2 et les postes (la gateway est la première adresse utilisable).
2. Ajoute les static routes nécessaires (compétence du lab 03).
3. Vérifie que chaque poste joint tous les autres.

## Partie 3 — Script Python `vlsm_planner.py`

Écris toi-même un script qui :

1. prend un bloc (par exemple `172.16.0.0/23`) et une liste de besoins (`nom, hôtes`) ;
2. trie les besoins du plus grand au plus petit ;
3. attribue à chacun le plus petit subnet suffisant, à la suite ;
4. affiche un tableau Markdown : nom, réseau/CIDR, masque, première et dernière adresse, diffusion, gateway ;
5. s'arrête avec un message clair si le bloc est trop petit.

Contraintes :

- utiliser uniquement la bibliothèque standard (module `ipaddress`) ;
- l'exécuter sous Linux (`python3 vlsm_planner.py`) ;
- ajouter au moins deux tests (`test_vlsm_planner.py`, avec `unittest` ou `pytest`) : le cas de ce lab, et un bloc trop petit.

## Vérifications attendues

| Vérification | Résultat attendu |
|---|---|
| Tableau calculé à la main = sortie du script | Identiques, ligne par ligne |
| Pings entre les 4 réseaux | 100 % de réussite |
| Tests Python | Tous réussis |

## Panne à provoquer

Donne à PC-LABO un masque /26 au lieu du masque prévu. Observe quels pings échouent ou réussissent et explique pourquoi avec le calcul binaire.

## Questions d'entretien

1. Combien d'hôtes utilisables dans un /27 ? Un /30 ?
2. Pourquoi attribuer les plus grands subnets en premier ?
3. Quelle est l'adresse de diffusion de 172.16.1.128/26 ?
4. Qu'apporte un script par rapport au calcul à la main ?

## Livrables (après réalisation dans Packet Tracer)

- `lab-04.pkt` : ta topologie finale, fonctionnelle.
- `configs/` : `show running-config` de chaque équipement, mots de passe remplacés par `<expurgé>`.
- `captures/` : captures des vérifications clés et de la panne corrigée.
- Sections ci-dessous remplies avec tes propres mots.

## Résultats

> À remplir : ce qui fonctionne, les sorties importantes, les tests réussis.

## Panne : diagnostic et correction

> À remplir : symptôme observé, commandes utilisées, cause trouvée, correction appliquée.

## Ce que j'ai appris

> À remplir : 3 à 5 points.
