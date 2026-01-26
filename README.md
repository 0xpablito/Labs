# Infrastructure L2/L3 - Segmentation & Redondance

## 1. Vue d'ensemble
Déploiement d'une architecture réseau hiérarchique. L'objectif est de valider la mise en place d'un cœur de réseau redondant et d'une segmentation stricte du trafic.
<img width="1756" height="648" alt="image" src="https://github.com/user-attachments/assets/5bfc457e-1868-47b4-833b-c3e91c476274" />


## 2. Implémentation technique

### Phase 1 : Configuration de base et Sécurité
Cette étape définit le socle de sécurité indispensable avant le déploiement des services réseau.
* Cette étape définit le socle de sécurité indispensable avant le déploiement des services réseau.
* Accès Distant : Migration vers SSHv2 (chiffrement RSA 1024 bits) et désactivation du protocole Telnet. 🔑
* Identité & Accès : Création d'un compte admin local et protection du mode privilégié par hachage MD5. 🛡️
* Management : Configuration d'une interface SVI dédiée pour l'administration IP de l'équipement. 🌐
* Confort CLI : Activation du logging synchronous pour éviter les interruptions lors de la saisie des commandes. ⌨️
* 🔗 [Consulter le script de base](./configs/01_base_setup.txt)
### Phase 2 : Segmentation VLAN & Routage Inter-VLAN
Mise en place d'une isolation logique des services et centralisation du routage sur le cœur de réseau via une architecture hybride.

🏢 Côté Siège (Switch L3)
* Segmentation : VLANs 10 (Admin), 20 (Prod), 30 (Sales), 40 (Guest).
* Routage SVI : Interfaces virtuelles sur le Switch L3 pour un routage inter-VLAN à vitesse filaire.
* Optimisation : Activation du PortFast sur les ports d'accès pour une connectivité instantanée des postes de travail.
* 🔗 [Consulter le script de base](./configs/02.1_vlan_config.txt)

🚚 Côté Opérations (Router-on-a-Stick)
* Segmentation : VLANs 70 (Partners) et 80 (Logistics).
* Routage Sub-interfaces : Utilisation du routeur central pour segmenter les flux opérationnels via le protocole 802.1Q.
* Lien Trunk : Configuration d'un lien d'agrégation entre le switch d'accès et le routeur pour transporter plusieurs VLANs sur un seul câble.
* 🔗 [Consulter le script de base](./configs/02.2_vlan_config.txt)

🛡️ Sécurité Réseau Globale
* VLAN Natif (VLAN 99) : Migration du trafic non tagué vers un VLAN dédié sur tous les Trunks (Switchs et Routeur) pour contrer le VLAN Hopping.
* VLAN BlackHole (VLAN 999) : Redirection de tous les ports inutilisés vers un VLAN isolé avec extinction administrative (shutdown).
🔗 Consulter le script de configuration complet (VLAN/ROAS)
