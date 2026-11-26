# CCNA Professional Labs

Portfolio de laboratoires réseau réalisés sous **Cisco Packet Tracer 8.2** pour préparer la certification **Cisco CCNA 200-301**.

Chaque lab est conçu, câblé, configuré, testé et dépanné par mes soins, puis documenté : objectif, topologie, plan d'adressage, configurations, tests de validation, panne diagnostiquée et conclusion. Les labs suivent la progression du cours *CCNA 200-301 Complete Course* de Jeremy's IT Lab (Days 1 à 30), du plus simple au plus complet.

**FILS EMMANUEL RODRIGUE MINYEM** — Abidjan, Côte d'Ivoire
[LinkedIn](https://linkedin.com/in/fils-emmanuel-minyem) · filsemma7@gmail.com

## Index des labs

| # | Lab | Days | Statut |
|---|-----|------|--------|
| 01 | [Configuration initiale et sécurisation de Cisco IOS](01-Network-Fundamentals/lab-01-configuration-initiale-ios) | 1–4, 9 | Réalisé |
| 02 | [Ethernet, ARP, vie d'un packet, TCP et UDP](01-Network-Fundamentals/lab-02-ethernet-arp-tcp-udp) | 3, 5–8, 10, 12, 30 | Réalisé |
| 03 | [Routage statique, default route et floating static route](06-Routing-Fundamentals/lab-03-routage-statique-flottant) | 11, 24 | À réaliser |
| 04 | [Plan d'adressage VLSM et script Python](02-IPv4-Subnetting/lab-04-plan-adressage-vlsm-python) | 7–8, 13–15 | À réaliser |
| 05 | [VLAN, trunks 802.1Q et router-on-a-stick](03-Switching-and-VLANs/lab-05-vlan-trunk-router-on-a-stick) | 16–17 | À réaliser |
| 06 | [Commutation multicouche, DTP et VTP](03-Switching-and-VLANs/lab-06-multicouche-dtp-vtp) | 18–19 | À réaliser |
| 07 | [STP et Rapid PVST+](04-STP-and-Redundancy/lab-07-stp-rapid-pvst) | 20–22 | À réaliser |
| 08 | [EtherChannel LACP, PAgP et couche 3](05-EtherChannel/lab-08-etherchannel-lacp-pagp) | 23 | À réaliser |
| 09 | [Dynamic routing : RIPv2, EIGRP puis OSPF](07-OSPF/lab-09-rip-eigrp-ospf) | 24–28 | À réaliser |
| 10 | [Projet de synthèse : réseau de campus multisite](13-Enterprise-Network-Architecture/lab-10-campus-multisite) | 1–30 | À réaliser |

## Compétences couvertes

| Compétence | Labs |
|------------|------|
| Modèles OSI et TCP/IP, Ethernet, ARP, MAC address table | 02 |
| TCP et UDP, ports, établissement de session | 02 |
| Cisco IOS en CLI, configuration initiale, sécurisation | 01, et tous les suivants |
| Adressage IPv4, subnets, CIDR, VLSM | 04, 10 |
| VLAN, access ports, trunks 802.1Q, VLAN natif | 05, 06, 10 |
| Routage inter-VLAN (router-on-a-stick, SVI) | 05, 06, 10 |
| DTP et VTP | 06 |
| STP et Rapid PVST+, PortFast, BPDU Guard | 07, 10 |
| EtherChannel (LACP, PAgP, couche 3) | 08, 10 |
| Static routes, par défaut, résumées et flottantes | 03, 10 |
| Dynamic routing RIPv2, EIGRP, OSPF | 09, 10 |
| Redondance de gateway (FHRP / HSRP) | 10 |
| Troubleshooting (ping, traceroute, commandes show) | tous |
| Python et Linux appliqués au réseau | 04, 10 |

## Structure d'un lab

```
lab-XX-nom/
├── README.md      objectif, topologie, plan d'adressage, tests, troubleshooting
├── lab-XX.pkt     maquette Packet Tracer
├── configs/       show running-config de chaque équipement
└── captures/      captures des vérifications
```

## Conventions

- **Aucun secret publié** : dans les configurations exportées, les mots de passe et les clés sont remplacés par `<expurgé>`.
- Adresses publiques : uniquement les plages réservées à la documentation (`203.0.113.0/24`, `198.51.100.0/24`).
- Seules mes propres topologies, configurations et documentation figurent ici. Les fichiers d'origine des cours suivis ne sont pas republiés.
- Packet Tracer ne reproduit pas toutes les commandes d'IOS. Quand une commande est refusée, l'équivalent utilisé est noté dans le README du lab.

## Environnement

Cisco Packet Tracer 8.2.2 · Routers ISR4331 · Switches 2960-24TT et 3650-24PS · Python 3 · Linux · Git
