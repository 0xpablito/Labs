# Infrastructure L2/L3 - Segmentation & Redondance

## 1. Vue d'ensemble
Déploiement d'une architecture réseau hiérarchique. L'objectif est de valider la mise en place d'un cœur de réseau redondant et d'une segmentation stricte du trafic.
<img width="1622" height="699" alt="image" src="https://github.com/user-attachments/assets/2b52325c-de37-4860-94b2-6c4d41e8af40" />




## 2. Implémentation technique

### Phase 1 : Configuration de base et Sécurité
Déploiement d'une configuration de base uniformisée et verrouillage des accès pour garantir l'intégrité et la gestion sécurisée du parc.

* Cette étape définit les bases de sécurité indispensables avant le déploiement des services réseau.
* Accès Distant : Migration vers SSHv2 (chiffrement RSA 1024 bits) et désactivation du protocole Telnet. 
* Identité & Accès : Création d'un compte admin local et protection du mode privilégié par hachage MD5. 
* Management : Configuration d'une interface SVI dédiée pour l'administration de l'équipement. 
* Confort CLI : Activation du logging synchronous pour éviter les interruptions lors de la saisie des commandes. 

🔗 [Consulter le script de base](./configs/01_base_setup.md)
🧪 [Consulter les tests de validation](./tests/01_base_setup.md)

### Phase 2 : Segmentation VLAN & Routage Inter-VLAN
Mise en place d'une isolation logique des services et centralisation du routage sur le cœur de réseau via une architecture hybride.

 Côté Siège (Switch L3)
* Segmentation : VLANs 10 (Admin), 20 (Prod), 30 (Sales), 40 (Guest).
* Routage SVI : Interfaces virtuelles sur le Switch L3 pour un routage inter-VLAN à vitesse filaire.
* Optimisation : Activation du PortFast sur les ports d'accès pour une connectivité instantanée des postes de travail.

 🔗 [Consulter le script de base](./configs/02.1_vlan_config.md)
 🧪 [Consulter les tests de validation](./tests/02.1_VLAN.md)

 Côté Opérations (Router-on-a-Stick)
* Segmentation : VLANs 70 (Partners) et 80 (Logistics).
* Routage Sub-interfaces : Utilisation du routeur central pour segmenter les flux opérationnels via le protocole 802.1Q.
* Lien Trunk : Configuration d'un lien d'agrégation entre le switch d'accès et le routeur pour transporter plusieurs VLANs sur un seul câble.

 🔗 [Consulter le script de base](./configs/02.2_vlan_config.md)
 🧪 [Consulter les tests de validation](./tests/02.2_VLAN.md)

 Sécurité Réseau Globale
* VLAN Natif (VLAN 99) : Migration du trafic non tagué vers un VLAN dédié sur tous les Trunks (Switchs et Routeur) pour contrer le VLAN Hopping.
* VLAN "BlackHole" (VLAN 999) : Redirection de tous les ports inutilisés vers un VLAN isolé avec extinction administrative (shutdown).

### Phase 3 : Haute Disponibilité & Performance Réseau
Optimisation des liaisons physiques et sécurisation de la topologie pour garantir une infrastructure résiliente face aux pannes.

* Agrégation de Liens (LACP) : Création de liens agrégés (Port-Channels) entre le Switch L3 et les switchs d'accès pour augmenter la bande passante et offrir une redondance matérielle.
* Interopérabilité : Utilisation du protocole LACP (IEEE 802.3ad). Ce choix garantit l'interopérabilité avec des équipements multi-constructeurs.
* Négociation Dynamique : Configuration en mode Active pour permettre une détection automatique des erreurs et une agrégation sécurisée des liens physiques.
* Sécurisation Spanning-Tree : Déploiement du BPDU Guard et du Root Guard pour éviter les boucles ou les switchs malveillants.
* Optimisation STP : Activation du PortFast sur tous les ports utilisateurs. Cela permet aux PC d'accéder au réseau immédiatement (en sautant les 30 secondes d'attente du Spanning-Tree) dès qu'ils sont branchés.

🔗 [Consulter le script de base](/configs/03_Etherchannel.md) 
🧪 [Consulter les tests de validation](/tests/03_Etherchannel.md)

### Phase 4 : Services IP & Connectivité WAN

#### 1. Adressage Dynamique (DHCP)
* Mise en place de serveurs DHCP pour automatiser l'attribution des adresses IP.
* Côté Siège (Switch L3) : Création des pools pour les VLANs 10, 20, 30 et 40.
* Côté Dépot (Routeur) : Création des pools pour les VLANs 70 et 80.
* Exclusions : Réservation des 5 premières adresses de chaque pool pour les équipements statiques (passerelles, imprimantes).
* Étendue des services : Distribution automatique de l'adresse IP, du masque, de la passerelle et du serveur DNS (8.8.8.8).

#### 2. Sécurité et Contrôle d'Accès (ACL)
* Mise en place de filtres pour sécuriser l'infrastructure et restreindre l'accès aux ressources sensibles.
* Isolation de l'imprimante : Utilisation d'une ACL étendue (101) pour interdire au VLAN 40 (Guest) de communiquer avec l'imprimante de production (192.168.20.2).
* Filtrage NAT : Définition d'une liste de réseaux autorisés à sortir vers l'extérieur.
* Sécurité Management : Restriction des accès VTY (SSH) pour autoriser uniquement l'adresse IP de l'administrateur.

#### 3. Translation d'Adresses (NAT/PAT)
* Mise en place de la connectivité vers le monde extérieur via le routeur de sortie.
* NAT Overload (PAT) : Traduction de l'ensemble des adresses privées du réseau vers l'adresse publique unique de l'interface Serial.
* Segmentation Inside/Outside : Marquage des interfaces locales comme "inside" et de l'interface WAN comme "outside" pour activer le moteur de translation.
